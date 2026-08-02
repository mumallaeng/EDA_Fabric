# macOS Apple Silicon Discovery Record

Observed date: `2026-08-02`

## Scope

This record captures the read-only result of `./scripts/eda-fabric doctor` on the current macOS host. It is discovery evidence, not a support certification.

## Observed Host

| Field | Observed value |
| --- | --- |
| Operating system | `Darwin` |
| CPU architecture | `arm64` |
| Python | `3.14.6` |
| Output root | `out/` did not exist and was not created by `doctor` |

## Local Free-Tool Discovery

| Tool | Observed state | Version or note |
| --- | --- | --- |
| Verilator | `available` | `5.048` |
| Icarus Verilog | `available` | `13.0` |
| GTKWave | `unavailable` | executable not found |
| Yosys | `unavailable` | executable not found |
| Verible syntax tool | `unavailable` | executable not found |

The next common simulation implementation uses Verilator. Icarus Verilog remains an optional compatibility adapter. No missing tool should be installed until the user approves the observed baseline and target scope.

## Declared, Unprobed Lanes

| Lane | Declared tools | Status |
| --- | --- | --- |
| macOS Linux virtual environment | Vivado | `declared-not-probed` |
| remote Windows | Vivado | `declared-not-probed` |
| separate remote Linux | VCS, Verdi, UVM | `declared-not-probed` |

The discovery command did not contact these lanes or inspect connection data.
