# Native renderer port: status

This page records where the native Windows port of Donkey Kong Bananza stands. It is a status note, not a plan.
The figures are project measurements taken on one machine (RTX 3070 Ti laptop, Windows 11), and they will
move as work lands.

## What ships today

Gameplay still renders by replaying the console's NVN command buffers through the Vulkan back end
(`runtime/nvn_api_*.c`, `runtime/vk_exec*.c`). A native request path exists in `runtime/native_render.c`
(request version 5). It has run only on synthetic and captured-pass probes. The game does not feed it yet.

## What is measured

- Shader conversion: all 18,825 NVN 2 stages in the census translate. The set of legal variants per
  family is not established, so this is not a completeness claim.
- Lowering: the Ampere forms covered by `tests/native_*_test.cpp` pass at `-O0` and `-O2`. These tests check the
  lowered output, not the rendering.
- Texture inventory: `tools/native_asset_*.py` converts the BNTX records with a coverage ledger. No game code loads the
  output yet.
- CPU coverage: over six recorded gameplay captures, 1,930 of 20,791 computed-branch sites were observed (9.3%).
  The rest are unknown, not safe. No trap site was reached in the four runs with trap telemetry.

## What is not met

- Acceptance: 0 of 15 gates pass.
- First visit to new content: shader translation waits of up to about 8 seconds were measured on first visits to
  new areas. Repeat visits show no translation wait.
- Frame rate: on warmed repeat visits of one scripted route, the 1% low was 22 to 33 fps against a goal of 55 fps.
- Default-on fallbacks: several skips and substitutions are on by default, for example the light-quad skip on
  NVIDIA. Their counts are not in the default logs yet.

## How to measure

`tools/perf_run.ps1` runs a `dkb.exe` with a fresh or copied profile and writes an evidence bundle.
`tools/perf_evidence.py` turns a bundle into frame-time statistics and one verdict per threshold. A verdict of
NOT-MEASURED means the run did not cover that threshold. See `tools/perf_run.README.md`.
