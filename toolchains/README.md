# Toolchain Adapters

An adapter translates a target manifest into one tool invocation and writes all generated state below `out/<target>/<adapter>/<build-id>/`.

`remote-lanes.json` contains only non-secret declared routing information. It must not contain a hostname, address, username, credential, session token, or license value. A lane becomes usable only after an explicit preflight.
