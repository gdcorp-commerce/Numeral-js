# Numeral-js Devcontainer

Node.js (lts) base devcontainer — no backend services; Numeral.js is a pure formatting/manipulation library with no runtime dependencies.

## What is this?

A **devcontainer** is a pre-configured development environment that runs in Docker. Once set up, `atlas-container start` gives you a fully wired container with the project toolchain, AI coding agents, and all dependencies ready to go — no local language/runtime setup, no manual credential juggling.

📖 **Quick start guide:** [Code Atlas - Containers](https://godaddy-corp.atlassian.net/wiki/spaces/SELLMORE/pages/4447535626/Code+Atlas+-+Containers)

---

## Starting the container

**Option A — atlas-container CLI (recommended):**
```sh
atlas-container start          # start or attach (from repo root)
atlas-container shell          # open a bash shell in a running container
atlas-container stop           # stop the container (preserves state)
atlas-container rebuild        # rebuild the image from scratch
atlas-container rm             # remove the container (keeps cached images)
atlas-container rm --purge     # full cleanup including cached images
```

Install once: `npm install -g @code-atlas/atlas-container --registry=https://gdartifactory1.jfrog.io/artifactory/api/npm/node-virt/`

First-time setup: `atlas-container setup && atlas-container login`

**Option B — VS Code:**
`F1` → "Dev Containers: Reopen in Container"

**Option C — JetBrains Gateway:**
Gateway → Remote Development → Dev Containers → Open Dev Container

---

## Running the app

Numeral.js is a library, not a server — there's nothing to "run," only build and test.

Install: `npm install`
Test: `npm test` (runs `grunt` default task: jshint + nodeunit)
Build (minified dist + concatenated languages.js): `npx grunt build`

---

## Regenerating

```sh
/gdc:scaffold-devcontainer
```

Then run `atlas-container rebuild` to pick up the updated `devcontainer.json`.
