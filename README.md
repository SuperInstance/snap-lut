# snap-lut — Pythagorean Triple Snap Lookup Table for FPGA

One BRAM block. Zero floating point. Deterministic WCET.

Maps any angle to the nearest Pythagorean triple angle using a pre-computed
lookup table stored in FPGA block RAM. No CPU, no OS, no floating point unit.

## What It Does

- **1024-entry BRAM**: 10-bit angle input → snapped angle + triple_c + margin
- **Q4.12 fixed point**: no FP needed on the FPGA
- **65 Pythagorean triples** pre-computed by Python generator
- **Plain Verilog-2001**: compatible with yosys, nextpnr, any FPGA flow
- **C header included**: same LUT works on ESP32, STM32, or any MCU

## Utilization (iCE40UP5K)

| Resource | Used | Total | %   |
|----------|------|-------|-----|
| LUTs     | 84   | 5,280 | 1%  |
| BRAM     | 12   | 30    | 40% |
| FFs      | 47   |       |     |

## Use Cases

- Safety-critical constraint snapping with deterministic timing
- Robot arm workspace bounds (snap joint angles to safe manifold)
- Sonar beam steering (snap beam angles to Eisenstein lattice)
- Any application where floating point is unavailable or uncertifiable

## Quick Start

```bash
python3 generate_snap_table.py  # generates hex files
yosys synth_snap.ys             # synthesize for iCE40
nextpnr-ice40 --up5k --package sg48 --json constraint_snap_top.json --asc constraint_snap_top.asc
```

## Files

| File | Description |
|------|-------------|
| `generate_snap_table.py` | Python generator — computes triples, emits hex + C header |
| `snap_lut.v` | Verilog BRAM module with 1024-entry lookup |
| `constraint_snap_top.v` | Top-level wrapper for synthesis |
| `synth_snap.ys` | Yosys synthesis script |
| `snap_lut.hex` | BRAM init: snapped angle + triple_c values |
| `snap_margin.hex` | BRAM init: snap margin (distance to nearest triple) |
| `snap_lut.h` | C header — same LUT data for MCU use |

## Composable With

- **[eisenstein-cuda](https://github.com/SuperInstance/eisenstein-cuda)**: same math, GPU version
- **[fleet-proto](https://github.com/SuperInstance/fleet-proto)**: same constraint types, Rust version
- **[cocapn-schemas](https://github.com/SuperInstance/cocapn-schemas)**: same tile format for PLATO publishing

## License

Apache 2.0
