# No_Memory Netlist Inventory

## Scope

This inventory covers the designs in:

```text
/nfs/share/home/huangzhipeng/code-new/benchmark_dataset/netlist/ics55/20260602/No_Memory
```

The scan found **78 designs**, identified by the presence of a `design.json`
file. For every design, `design.json` supplies the authoritative top module,
clock, target frequency, fixed netlist, SDC, and ICS55 library/LEF paths.

All 78 designs were validated to have both the referenced
`netlist_paths.fixed_netlist` and `netlist_paths.sdc` files. Their
`netlist_dataset.has_memory` fields are all `false`.

## Handoff Rules

- Treat every row below as one independent backend-design job.
- Use the `fixed_netlist` and `sdc` paths specified by that design's
  `design.json`; do not derive inputs from a naming convention.
- Use `top`, `clock`, and `recommended_frequency_mhz` from the same metadata
  file when preparing the backend flow.
- The PDK and standard-cell library/LEF paths are also in `design.json` under
  `yosys_flow_libraries`. No macro library inputs are expected for this
  `No_Memory` dataset.

## Design Counts

| Instance bucket | Designs |
| --- | ---: |
| `lt_200k` | 73 |
| `200k_500k` | 2 |
| `500k_1000k` | 1 |
| `1000k_2000k` | 2 |
| **Total** | **78** |

## Designs

`Path` is relative to the dataset root above. `Instances` excludes
`scopeinfo` cells and can be used to schedule the batch from small to large.

| Design | Top | Clock | Freq. (MHz) | Instances | Path |
| --- | --- | --- | ---: | ---: | --- |
| `rocket_chip` | `ExampleRocketSystem` | `io_aggregator_0_clock` | 100 | 1,014,574 | `1000k_2000k/rocket_chip` |
| `rocketchip_std` | `ExampleRocketSystem` | `io_aggregator_0_clock` | 100 | 1,040,301 | `1000k_2000k/rocketchip_std` |
| `veer_el2` | `veer_wrapper` | `clk` | 100 | 236,539 | `200k_500k/veer_el2` |
| `ysyx_22040886` | `ysyx_22040886` | `clock` | 100 | 287,187 | `200k_500k/ysyx_22040886` |
| `sram-main` | `axi4_sram_fsm` | `aclk` | 100 | 558,135 | `500k_1000k/sram-main` |
| `BM64` | `BM64` | `Clk` | 100 | 22,210 | `lt_200k/BM64` |
| `PPU` | `PPU` | `clk` | 250 | 9,326 | `lt_200k/PPU` |
| `SimpleEdgeAiSoC` | `ip1_SimpleEdgeAiSoC` | `clock` | 140 | 75,461 | `lt_200k/SimpleEdgeAiSoC` |
| `TJUT_TOP` | `ip2_TJUT_TOP` | `clk` | 170 | 11,386 | `lt_200k/TJUT_TOP` |
| `aes` | `aes_cipher_top` | `clk` | 350 | 11,492 | `lt_200k/aes` |
| `arm9` | `arm9_compatiable_code` | `clk` | 105 | 21,062 | `lt_200k/arm9` |
| `blake2s` | `blake2s` | `clk` | 90 | 25,916 | `lt_200k/blake2s` |
| `can_top` | `can_top` | `clk` | 245 | 84,095 | `lt_200k/can_top` |
| `cv32e40p` | `cv32e40p_top` | `clk_i` | 105 | 36,187 | `lt_200k/cv32e40p` |
| `double_fpu` | `fpu` | `clk` | 210 | 48,983 | `lt_200k/double_fpu` |
| `eth_top` | `eth_top` | `wb_clk_i` | 100 | 32,368 | `lt_200k/eth_top` |
| `fft` | `FFT` | `clock` | 210 | 20,301 | `lt_200k/fft` |
| `floonoc` | `floo_alu_top` | `clk_i` | 90 | 9,283 | `lt_200k/floonoc` |
| `i2s-main` | `apb4_i2s_wrapper` | `pclk` | 325 | 13,180 | `lt_200k/i2s-main` |
| `ibex` | `ibex_top` | `clk_i` | 120 | 15,521 | `lt_200k/ibex` |
| `ieda_chips__ysyx_cpu__ysyx_210092_if_top` | `ysyx_210092` | `clock` | 150 | 28,952 | `lt_200k/ieda_chips__ysyx_cpu__ysyx_210092_if_top` |
| `ieda_chips__ysyx_cva6_sv` | `ysyx_cva6` | `clock` | 90 | 155,738 | `lt_200k/ieda_chips__ysyx_cva6_sv` |
| `ieda_chips__ysyx_darkriscv_v__darkuart` | `ysyx_darkriscv` | `clock` | 105 | 39,017 | `lt_200k/ieda_chips__ysyx_darkriscv_v__darkuart` |
| `jpeg_encoder` | `jpeg_encoder` | `clk` | 180 | 66,172 | `lt_200k/jpeg_encoder` |
| `openlane2_ci_designs__aes` | `aes` | `clk` | 200 | 17,380 | `lt_200k/openlane2_ci_designs__aes` |
| `openlane2_ci_designs__apu` | `APU` | `clk` | 375 | 3,514 | `lt_200k/openlane2_ci_designs__apu` |
| `openlane2_ci_designs__ms_spi_xip_cache` | `MS_SPI_XIP_CACHE_ahbl` | `HCLK` | 395 | 6,393 | `lt_200k/openlane2_ci_designs__ms_spi_xip_cache` |
| `openlane2_ci_designs__xtea` | `xtea` | `clock` | 250 | 3,050 | `lt_200k/openlane2_ci_designs__xtea` |
| `openpiton` | `dynamic_node_top_wrap` | `clk` | 305 | 8,808 | `lt_200k/openpiton` |
| `openroad_flow_scripts__flow_designs_src_aes` | `aes_inv_cipher_top` | `clk` | 315 | 16,366 | `lt_200k/openroad_flow_scripts__flow_designs_src_aes` |
| `openroad_flow_scripts__flow_designs_src_ibex_sv` | `ibex_core` | `clk_i` | 100 | 15,018 | `lt_200k/openroad_flow_scripts__flow_designs_src_ibex_sv` |
| `openroad_flow_scripts__flow_designs_src_jpeg` | `jpeg_encoder` | `clk` | 180 | 66,172 | `lt_200k/openroad_flow_scripts__flow_designs_src_jpeg` |
| `opentitan__hw_ip_aes` | `aes` | `clk_i` | 110 | 65,777 | `lt_200k/opentitan__hw_ip_aes` |
| `opentitan__hw_ip_csrng` | `csrng` | `clk_i` | 180 | 38,213 | `lt_200k/opentitan__hw_ip_csrng` |
| `opentitan__hw_ip_edn` | `edn` | `clk_i` | 310 | 11,050 | `lt_200k/opentitan__hw_ip_edn` |
| `opentitan__hw_ip_entropy_src` | `entropy_src` | `clk_i` | 170 | 44,736 | `lt_200k/opentitan__hw_ip_entropy_src` |
| `opentitan__hw_ip_hmac` | `hmac` | `clk_i` | 105 | 45,861 | `lt_200k/opentitan__hw_ip_hmac` |
| `opentitan__hw_ip_keymgr` | `keymgr` | `clk_i` | 105 | 43,082 | `lt_200k/opentitan__hw_ip_keymgr` |
| `opentitan__hw_ip_otp_ctrl` | `otp_ctrl` | `clk_i` | 150 | 44,496 | `lt_200k/opentitan__hw_ip_otp_ctrl` |
| `opentitan__hw_ip_uart` | `uart` | `clk_i` | 415 | 3,841 | `lt_200k/opentitan__hw_ip_uart` |
| `opentitan_earl_grey` | `lc_ctrl` | `clk_i` | 210 | 11,124 | `lt_200k/opentitan_earl_grey` |
| `picorv32a` | `picorv32a` | `clk` | 200 | 14,202 | `lt_200k/picorv32a` |
| `poly1305` | `poly1305` | `clk` | 140 | 51,032 | `lt_200k/poly1305` |
| `prince` | `prince` | `clk` | 350 | 6,274 | `lt_200k/prince` |
| `salsa20` | `salsa20` | `clk` | 130 | 25,455 | `lt_200k/salsa20` |
| `serv` | `serv_rf_top` | `clk` | 395 | 4,412 | `lt_200k/serv` |
| `sha256` | `sha256` | `clk` | 170 | 12,808 | `lt_200k/sha256` |
| `sm4` | `sm4_top` | `clk` | 235 | 76,806 | `lt_200k/sm4` |
| `spi-main` | `apb4_spi_wrapper` | `pclk` | 350 | 7,266 | `lt_200k/spi-main` |
| `stage_b_ysyx_23060170` | `ysyx_23060170` | `clock` | 250 | 8,967 | `lt_200k/stage_b_ysyx_23060170` |
| `stage_b_ysyx_23060203` | `ysyx_23060203` | `clock` | 225 | 9,647 | `lt_200k/stage_b_ysyx_23060203` |
| `stage_b_ysyx_23060229` | `ysyx_23060229` | `clock` | 225 | 9,788 | `lt_200k/stage_b_ysyx_23060229` |
| `stage_b_ysyx_23060246` | `ysyx_23060246` | `clock` | 190 | 9,515 | `lt_200k/stage_b_ysyx_23060246` |
| `stage_b_ysyx_24070003` | `ysyx_24070003` | `clock` | 120 | 9,452 | `lt_200k/stage_b_ysyx_24070003` |
| `stage_b_ysyx_24100012` | `ysyx_24100012` | `clock` | 280 | 9,066 | `lt_200k/stage_b_ysyx_24100012` |
| `stage_b_ysyx_24110017` | `ysyx_24110017` | `clock` | 290 | 9,981 | `lt_200k/stage_b_ysyx_24110017` |
| `stage_b_ysyx_25010008` | `ysyx_25010008` | `clock` | 285 | 9,808 | `lt_200k/stage_b_ysyx_25010008` |
| `stage_b_ysyx_25010030` | `ysyx_25010030` | `clock` | 160 | 10,005 | `lt_200k/stage_b_ysyx_25010030` |
| `stage_b_ysyx_25020037` | `ysyx_25020037` | `clock` | 170 | 9,143 | `lt_200k/stage_b_ysyx_25020037` |
| `stage_b_ysyx_25040129` | `ysyx_25040129` | `clock` | 250 | 8,733 | `lt_200k/stage_b_ysyx_25040129` |
| `stage_d_ysyx_24080018` | `ysyx_24080018` | `clock` | 200 | 11,030 | `lt_200k/stage_d_ysyx_24080018` |
| `stage_d_ysyx_24090003` | `ysyx_24090003` | `clock` | 150 | 9,727 | `lt_200k/stage_d_ysyx_24090003` |
| `stage_d_ysyx_24090010` | `ysyx_24090010_exu` | `clock` | 160 | 9,530 | `lt_200k/stage_d_ysyx_24090010` |
| `stage_d_ysyx_25010009` | `ysyx_25010009` | `clock` | 235 | 9,492 | `lt_200k/stage_d_ysyx_25010009` |
| `stage_d_ysyx_25020042` | `ysyx_25020042` | `clock` | 175 | 8,232 | `lt_200k/stage_d_ysyx_25020042` |
| `stage_d_ysyx_25070194` | `ysyx_25070194` | `clock` | 165 | 8,203 | `lt_200k/stage_d_ysyx_25070194` |
| `stage_d_ysyx_25080201` | `ysyx_25080201` | `clock` | 100 | 6,937 | `lt_200k/stage_d_ysyx_25080201` |
| `tpu` | `tpu_top` | `clk` | 160 | 57,818 | `lt_200k/tpu` |
| `trng` | `trng` | `clk` | 90 | 49,372 | `lt_200k/trng` |
| `vexriscv` | `VexRiscv` | `clk` | 235 | 8,976 | `lt_200k/vexriscv` |
| `vga-main` | `axi4_vgalcd_wrapper` | `aclk` | 200 | 87,381 | `lt_200k/vga-main` |
| `y_huff` | `y_huff` | `clk` | 450 | 12,729 | `lt_200k/y_huff` |
| `ysyxSoCTest` | `ysyxSoCASIC` | `clock` | 120 | 28,805 | `lt_200k/ysyxSoCTest` |
| `ysyx_040091` | `ysyx_040091` | `clock` | 95 | 153,031 | `lt_200k/ysyx_040091` |
| `ysyx_040978` | `ysyx_040978` | `clock` | 100 | 58,743 | `lt_200k/ysyx_040978` |
| `ysyx_050228` | `ysyx_050228` | `clock` | 90 | 124,390 | `lt_200k/ysyx_050228` |
| `ysyx_050369` | `ysyx_050369` | `clock` | 80 | 105,596 | `lt_200k/ysyx_050369` |
| `ysyx_210134` | `ysyx_210134` | `clock` | 90 | 70,327 | `lt_200k/ysyx_210134` |
