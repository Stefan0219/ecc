# ECC Backend Benchmark Runbook

This runbook is for long-running gate-level backend benchmarks, especially
large ICS55 designs such as RocketChip. It covers workspace setup, tmux
operation, monitoring, and recovery. It does not replace the project setup
instructions in `docs/development.md`.

## Operating Rules

- Run every backend flow in a named tmux session. Do not leave a long flow
  attached to an agent terminal.
- Treat `benchmark_runs/` as generated local data. It is ignored by Git and
  must not be committed.
- Before starting another large run, inspect existing tmux sessions, active
  processes, and available memory. Do not stop another user's process unless
  explicitly directed to do so.
- Use a new workspace directory for every parameter point. Never reuse or
  clear an existing result directory in place.
- Check the persisted `home/flow.json` and the process table together. A step
  can remain `Ongoing` after its process has been terminated.

## Environment

From the repository root, use the local virtual environment after it has been
synced. When Nix is available, enter the development shell first.

```bash
nix develop
uv sync --no-build-isolation-package ecc-dreamplace \
  --no-build-isolation-package ecc-tools-bin --verbose
source .venv/bin/activate
```

For an already prepared checkout, `.venv/bin/python` is sufficient. Set a
writable Matplotlib cache directory for processes that import plotting code:

```bash
export MPLCONFIGDIR=/tmp/ecc_matplotlib
```

## Preflight

Choose explicit names for the workspace and tmux session. The session name
must identify the parameter point, for example `rocketchip_150m_d055`.

```bash
tmux list-sessions
free -h
ps -eo pid,etime,stat,pcpu,pmem,rss,args | rg 'rocket_chip|iRT|chipcompiler'
test ! -e benchmark_runs/rocket_chip/<new-run-name>
```

Routing a million-instance design can use hundreds of GiB of RSS and dozens
of CPU cores. Available memory, not merely free memory, must leave enough
headroom for all active runs.

## Create A Fresh Parameter Point

For RocketChip, begin from a known-good workspace so its resolved PDK views
(including the H7CH LEF and Liberty files) are preserved. Copy the input
gate-level netlist only; do not copy flow state or step outputs.

Change every requested parameter before calling `create_workspace`. Use a
directory name that records the material overrides.

```python
from copy import deepcopy

from chipcompiler.data import StateEnum, create_workspace, load_workspace
from chipcompiler.engine import EngineFlow

baseline = load_workspace("benchmark_runs/rocket_chip/freq_100mhz_density_0.20")
parameters = deepcopy(baseline.parameters)
parameters.path = None
parameters.data.update(
    {
        "Frequency max [MHz]": 150,
        "Target density": 0.55,
        "Target overflow": 0.05,
        "Max fanout": 10,
        "Cell padding x": 100,
        "Global right padding": 100,
        "Bottom layer": "MET1",
    }
)

pdk = deepcopy(baseline.pdk)
# Regenerate the SDC when changing frequency. If the source SDC has additional
# timing constraints, copy and update those constraints instead of using this.
pdk.sdc = None

workspace = create_workspace(
    directory="benchmark_runs/rocket_chip/freq_150mhz_density_0.55_fanout_10_padding_100_met1",
    origin_def="",
    origin_verilog=baseline.design.origin_verilog,
    pdk=pdk,
    parameters=parameters,
)
if workspace is None:
    raise RuntimeError("workspace already exists or could not be created")

flow = EngineFlow(workspace)
for name, tool in (
    ("Floorplan", "ecc"),
    ("fixFanout", "ecc"),
    ("place", "dreamplace"),
    ("CTS", "ecc"),
    ("legalization", "dreamplace"),
    ("route", "ecc"),
    ("drc", "ecc"),
    ("filler", "ecc"),
):
    flow.add_step(name, tool, StateEnum.Unstart)
```

Verify the newly generated data before launching the flow:

```bash
jq '.' benchmark_runs/rocket_chip/<new-run-name>/home/parameters.json
sed -n '1,20p' benchmark_runs/rocket_chip/<new-run-name>/origin/rocket_chip.sdc
jq '.steps | map({name, tool, state})' \
  benchmark_runs/rocket_chip/<new-run-name>/home/flow.json
jq '{target_density, stop_overflow, cell_padding_x, lef_input}' \
  benchmark_runs/rocket_chip/<new-run-name>/config/dreamplace.json
```

When a frequency changes, verify the SDC period as well as the frequency
field. A 150 MHz clock requires a period of approximately 6.667 ns.

## Start A Full Flow In tmux

Use a short launcher script or Python command that loads the fresh workspace,
creates its step workspaces, and runs the flow. Start it in tmux before
detaching.

```python
from chipcompiler.data import load_workspace
from chipcompiler.engine import EngineFlow

workspace = load_workspace("benchmark_runs/rocket_chip/<new-run-name>")
flow = EngineFlow(workspace)
flow.create_step_workspaces()
if not flow.run_steps():
    raise SystemExit(1)
```

Example tmux lifecycle:

```bash
tmux new-session -d -s <session-name> -c "$PWD"
tmux send-keys -t <session-name>:0 \
  'MPLCONFIGDIR=/tmp/ecc_<session-name> .venv/bin/python <launcher.py>' C-m
tmux list-windows -t <session-name> -F '#S:#I:#W #{pane_current_command} #{pane_pid}'
tmux attach -t <session-name>
```

Use a launcher outside the repository or an explicitly requested tracked
script. Do not create an untracked helper in the repository just to start one
run.

## Monitor A Run

`home/flow.json` is the source of truth for completed steps. Per-step logs
show fine-grained progress. Read both.

```bash
jq -r '.steps[] | [.name, .state, .runtime, .["peak memory (mb)"]] | @tsv' \
  benchmark_runs/rocket_chip/<run-name>/home/flow.json

tail -n 40 benchmark_runs/rocket_chip/<run-name>/route_ecc/data/rt/rt.log
tail -n 40 benchmark_runs/rocket_chip/<run-name>/place_dreamplace/log/place.log

tmux capture-pane -t <session-name>:0 -p -S -80
ps -p <tmux-pane-pid> -o pid,etime,stat,pcpu,pmem,rss,args
free -h
```

Large router phases may go many hours without a new progress line. Treat a
process with sustained CPU usage as active even when the log timestamp is old.
Conversely, an `Ongoing` state with no matching process is interrupted work,
not active work.

Some ECC logs contain repeated `No driver pin exist...` messages while writing
outputs. Do not classify a step as failed from that message alone. Check the
final flow state and the expected DEF, Verilog, and GDS outputs.

## Resume A Dangling Route Step

If `route` is `Ongoing`, no route process exists, and no route output was
produced, restart only the route step. Do not batch-recreate every remaining
step before initializing the database: the shared `db_default_config.json`
can otherwise be rewritten with a downstream step's inputs.

```python
from pathlib import Path

from chipcompiler.data import load_workspace
from chipcompiler.engine import EngineFlow
from chipcompiler.tools import create_step

workspace = load_workspace("benchmark_runs/rocket_chip/<run-name>")
root = Path(workspace.directory)
route = create_step(
    workspace=workspace,
    step="route",
    eda="ecc",
    input_def=root / "legalization_dreamplace/output/rocket_chip_legalization.def.gz",
    input_verilog=root / "legalization_dreamplace/output/rocket_chip_legalization.v.gz",
    input_db=root / "legalization_dreamplace/output/rocket_chip_legalization_db",
    initialize_config=True,
)
if route is None:
    raise RuntimeError("could not prepare route")

flow = EngineFlow(workspace)
flow.workspace_steps = [route]
if not flow.run_steps():
    raise SystemExit(1)
```

Run this recovery in a new tmux session. After route succeeds, prepare and run
DRC and filler deliberately; do not assume they were started by a route-only
recovery.

## Results And Git

Results are under each step's `output/`, `report/`, `analysis/`, and `log/`
directories. The final successful flow has every item in `home/flow.json` set
to `Success`.

Before committing source changes, verify that generated data is excluded:

```bash
git status --short
git diff --check
```

Commit only source, tests, and documentation. Keep benchmark workspaces,
process IDs, and live progress reports out of version control.
