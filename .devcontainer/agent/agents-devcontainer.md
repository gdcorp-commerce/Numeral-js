# Devcontainer — Numeral-js

Running inside the Numeral-js devcontainer.

## Environment
- Base image: mcr.microsoft.com/devcontainers/base:ubuntu
- Language: Node.js (lts)
- Workspace: /workspace
- Running the app: library only — see README.md § Running the app (install/test/build, no run command)

## Credentials available (forwarded from host shell)
- ARTIFACTORY_EMAIL / ARTIFACTORY_TOKEN — npm (gd-artifactory)
- GOCODE_API_TOKEN / GOCODE_BASE_URL — AI coding agents (gocode LiteLLM proxy)

## Context files
| Topic | File |
|-------|------|
| Claude Code — credentials, denied ops, MCP wiring | [claude-code.md](claude-code.md) |
| Container safety — git mode, blocked hosts, MCPs | [devcontainer-commons.md](devcontainer-commons.md) |
| Artifactory registry config | [gd-artifactory.md](gd-artifactory.md) |
| Certs, Docker network, CA trust | [stunt-doubles-core.md](stunt-doubles-core.md) |
