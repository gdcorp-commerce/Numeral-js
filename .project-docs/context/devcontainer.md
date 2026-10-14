# Devcontainer — Numeral-js
Base image + Node.js lts, no stunt-doubles services wired (pure library, no backend calls).

| | |
|---|---|
| Base image | mcr.microsoft.com/devcontainers/base:ubuntu |
| Start | `atlas-container start` or VS Code F1 → "Reopen in Container" |
| Shell | `atlas-container shell` |
| Host credentials | ARTIFACTORY_EMAIL, ARTIFACTORY_TOKEN, GIT_TOKEN |
| stunt CLI | `stunt ps` · `stunt up <service>` · `stunt down <service>` (inside container) |

In-container context: `.devcontainer/agent/agents-devcontainer.md` (auto-loaded by Claude Code inside the container).
