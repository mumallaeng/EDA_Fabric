# `eda-fabric doctor` Contract

## Purpose

`eda-fabric doctor` is a read-only preflight command. It reports the current repository, host, locally available free-tool executables, output-root policy, and declared remote capability lanes.

## Command

```sh
./scripts/eda-fabric doctor
```

The command writes JSON to standard output. It does not create `out/`, install software, edit shell configuration, contact a remote host, inspect credentials, or modify tracked files.

## Status Values

| Status | Meaning |
| --- | --- |
| `available` | executable was found and its version probe completed successfully |
| `unavailable` | executable was not found on the current host |
| `probe-failed` | executable was found but its version probe did not complete successfully |
| `declared-not-probed` | user-declared remote capability with no connection attempt |

## Acceptance Use

Run the command before any adapter implementation or test. Preserve its JSON output as test evidence only when the task requires it; do not add it to Git.
