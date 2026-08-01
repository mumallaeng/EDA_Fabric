# EDA_Fabric

Cross-platform environment for handling one hardware-design source contract safely across macOS, Windows, and Linux, while selectively running the EDA and firmware tools available on each host.

## Purpose

EDA_Fabric is an environment layer, not a replacement for any vendor IDE. It keeps source contracts portable, runs each toolchain through an explicit adapter, isolates generated workspaces, and connects hardware and firmware results through versioned handoff artifacts.

The same repository must support source review, editing, Git checks, manifest validation, and common HDL verification on every configured host. A host does not need to install or execute every vendor tool.

## Firmware toolchains are included

Firmware tools are part of this project, but they are a separate toolchain class from HDL EDA tools.

```text
HDL source + platform declaration
    -> HDL EDA adapter
    -> hardware handoff contract
    -> firmware adapter (for example, a vendor SDK tool)
    -> firmware artifact
```

A firmware adapter consumes an explicit hardware handoff contract containing platform identity, tool version, register and memory maps, and artifact hashes. It does not open, modify, or share an IDE workspace with the HDL adapter. This allows an HDL-only host, a firmware-capable host, and a board-programming host to be different machines while preserving the same source and artifact identity.

## Initial host policy

| Host | Initial role |
| --- | --- |
| macOS Apple Silicon | common source work and open HDL verification; vendor tools only through a supported remote path |
| Existing Windows test host | first local validation target for vendor HDL and firmware adapters |
| Linux | add later after the target distribution, architecture, tools, and board path are explicitly defined |

## Source and generated state

Git tracks reviewable inputs:

```text
hdl/            Verilog/SystemVerilog and testbenches
firmware/       portable firmware and target adapters
interfaces/     register, memory, interrupt, and clock contracts
platforms/      board/device declarations
constraints/    constraint sources and vendor mappings
manifests/      source sets, targets, tool requirements, and versions
toolchains/     common and vendor-specific adapters
```

Generated IDE workspaces, caches, logs, simulation products, synthesis outputs, firmware objects, and board images belong under the ignored `out/` build root or an external artifact store. They are never used as the shared source contract.

## First release boundary

1. Define the manifest and host-capability schema.
2. Run one common Verilog/SystemVerilog simulation path on macOS and Windows.
3. Add read-only capability discovery.
4. Add the first vendor HDL adapter on the Windows test host.
5. Add a separate firmware adapter consuming the hardware handoff contract.

Every adapter must report host OS, CPU architecture, tool version, required capability, input identity, output location, and whether the operation can mutate source-controlled files.
