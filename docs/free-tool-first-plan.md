# Free-Tool-First Implementation Plan

## Goal

Establish one reliable, no-license baseline before adding device-specific EDA, firmware SDK, or board adapters. The baseline proves that the source contract, command interface, host reporting, generated-output isolation, and Git checks work on macOS Apple Silicon and the existing Windows test host.

## Tool Order

| Order | Tool or capability | Role | Entry condition | Evidence of completion |
| ---: | --- | --- | --- | --- |
| 0 | Git and text contract | stable source checkout | repository exists | clone status and line-ending checks are clean |
| 1 | Host capability discovery | report OS, CPU, executable versions, and missing capabilities | no installation required | read-only report from macOS and Windows |
| 2 | Verilator | first common Verilog/SystemVerilog simulation path | host preflight identifies a usable installation | identical test manifest passes on both hosts |
| 3 | waveform artifact convention | preserve and locate simulation traces | Verilator path works | a waveform artifact is created below `out/` |
| 4 | GTKWave | optional local waveform viewer | waveform artifact exists | user can open the documented artifact path |
| 5 | Yosys | source-to-netlist synthesis experiment | common simulation path is stable | generated netlist and report stay below `out/` |
| 6 | Icarus Verilog | optional compatibility cross-check | Verilator baseline remains green | mismatch policy is documented from a real result |
| 7 | Windows vendor adapter | first device-specific HDL build | Windows capability report and tool version are confirmed | isolated vendor workspace passes clean-tree validation |
| 8 | firmware adapter | independent firmware build from a handoff contract | hardware handoff contract exists | firmware rejects mismatched handoff inputs |

No vendor tool, firmware SDK, board cable tool, or paid simulator is a dependency before phase 7.

## Declared Execution Lanes

The following lanes are user-declared capabilities. Each remains `declared` until a test preflight confirms reachability, host OS and CPU architecture, executable version, permission, and source checkout state.

| Lane | Declared capability | Use in this plan |
| --- | --- | --- |
| macOS local Linux virtual environment | Vivado execution | later vendor HDL adapter validation |
| Remote Windows | Vivado execution | Windows vendor HDL adapter and path-parity validation |
| Separate remote Linux | VCS, Verdi, and UVM execution | later licensed verification adapter validation |

The remote Linux verification lane is not part of the free-tool baseline. It becomes a separate capability-gated path after the common simulation contract is stable.

## Implementation Phases

### Phase 0: Repository Contract

Create `.gitattributes`, a small manifest schema, an ignored `out/` build root, and a source layout for `hdl/`, `manifests/`, `toolchains/`, and `scripts/`.

Acceptance criteria:

- `git status --short` remains empty after a clean clone.
- Text source uses a declared line-ending policy.
- Generated output is rejected from normal source tracking.

### Phase 1: Read-Only Host Discovery

Implement `eda-fabric doctor` as a read-only command. It reports the host OS, CPU architecture, repository revision, configured adapter names, executable paths and versions when found, and missing required capabilities. For a remote lane it also records the requested lane name and connection result without inspecting credentials. It must never install software, edit a shell profile, change a global setting, or create files outside `out/`.

Acceptance criteria:

- macOS Apple Silicon emits a capability report.
- The Windows test host later emits the same report schema.
- A missing tool is reported as `unavailable`, not as a failed build.

### Phase 2: Common Simulation Example

Add one small, self-contained Verilog/SystemVerilog design and testbench. Add a manifest that declares the top module, testbench, source order, expected result, and output root.

`eda-fabric test <target>` invokes the selected simulator only through the adapter. Generated C++, executable, log, and trace files go below `out/`.

Acceptance criteria:

- The same manifest and test result work on macOS and Windows.
- A failed test returns a readable failure report without changing tracked files.
- Test output identifies the simulator version and input manifest identity.

### Phase 3: Trace and Synthesis Evidence

Define an adapter-neutral waveform artifact location and add an optional GTKWave open command. Add a Yosys adapter that creates a netlist and report for the same source set without claiming a board-specific bitstream.

### Phase 4: Windows Parity Check

Run phases 1 through 3 on the existing Windows test host. Compare manifest identity, pass/fail outcome, output location, and clean-tree state. Record observed installation and version data instead of assuming a matching setup.

### Phase 5: Optional Vendor and Firmware Adapters

Add one vendor HDL adapter only after the free-tool baseline is stable. Add a firmware adapter only after a hardware handoff contract defines the platform, tool version, register map, memory map, interrupt map, and artifact hash.

## User Decision Points

| Decision | User owns | Codex implements after the decision |
| --- | --- | --- |
| first example behavior | choose a small HDL behavior worth testing | source, manifest, and testbench |
| baseline tools | approve the observed free tool installation set | adapter and capability checks |
| Windows toolchain | confirm what is installed and permitted | Windows profile and parity test |
| first vendor target | choose device family and board goal | vendor adapter design and validation |
| firmware scope | choose target and handoff contract content | isolated firmware adapter |

## Current Next Action

Phase 0 is implemented on `feat/free-tool-bootstrap`: source layout, text policy, manifest schema draft, declared remote lanes, and a read-only `doctor` command exist. The first macOS discovery result is recorded in [host-profiles/macos-apple-silicon.md](host-profiles/macos-apple-silicon.md).

Next, implement the first self-contained Verilator simulation target. Do not install or configure additional tools before the user approves the observed macOS baseline and the first example behavior.
