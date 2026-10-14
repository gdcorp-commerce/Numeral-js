## gd-artifactory — GoDaddy Artifactory package manager configuration

Configures one or more package managers to route dependency lookups through GoDaddy JFrog Artifactory at `gdartifactory1.jfrog.io`. Which managers are configured depends on the options set at image build time.

**Credentials** — sourced from the environment at tool runtime (never baked into the image):

| Env var | Purpose |
|---------|---------|
| `ARTIFACTORY_EMAIL` | Artifactory username / email |
| `ARTIFACTORY_TOKEN` | Artifactory identity token or API key |

These must be in `remoteEnv` in `devcontainer.json` so they are re-read from the host shell on every IDE reconnect.

**Configured package managers** (include only the rows matching what was configured):

| Manager | Registry URL | Config file |
|---------|-------------|-------------|
| [npm] npm / Yarn 1 | `https://gdartifactory1.jfrog.io/artifactory/api/npm/node-virt/` | `~/.npmrc` |
| [npm] Yarn Berry | same as npm | `~/.yarnrc.yml` |
| [maven] Maven | `https://gdartifactory1.jfrog.io/artifactory/java-virt` | `~/.m2/settings.xml` |
| [gradle] Gradle | `https://gdartifactory1.jfrog.io/artifactory/java-virt` | `~/.gradle/init.d/artifactory.gradle` |
| [pip] pip | `PIP_INDEX_URL` in `/etc/profile.d/gd-artifactory.sh` | environment variable |
| [poetry] Poetry | `POETRY_HTTP_BASIC_ARTIFACTORY_*` in `/etc/profile.d/gd-artifactory.sh` | environment variables |

**Check what is configured** (include only commands for configured managers):
```bash
# Which managers were enabled at image build time
cat /usr/local/share/gd-artifactory/post-create.sh

# [npm] npm registry
npm config get registry

# [maven] Maven settings
cat ~/.m2/settings.xml 2>/dev/null

# [gradle] Gradle init script
cat ~/.gradle/init.d/artifactory.gradle 2>/dev/null

# [pip] pip index
echo $PIP_INDEX_URL

# [poetry] Poetry credentials
echo $POETRY_HTTP_BASIC_ARTIFACTORY_URL
```

**Verify connectivity** — requires `ARTIFACTORY_EMAIL` and `ARTIFACTORY_TOKEN` to be set (include only commands for configured managers):
```bash
npm install --dry-run                    # [npm]
mvn dependency:resolve                   # [maven] run inside a project directory
gradle dependencies                      # [gradle] run inside a project directory
pip install --dry-run pip               # [pip]
poetry install --dry-run                # [poetry] run inside a project directory
```

For scoped npm packages from a team-specific Artifactory repo, add to the project's `.npmrc`:
```ini
@my-scope:registry=https://gdartifactory1.jfrog.io/artifactory/api/npm/my-repo-local/
```

**[maven] If a build reports "could not find artifact" for something not in Maven Central:** the feature's Maven mirror (`mirrorOf="*"`) routes every repo ID declared anywhere in the project's POMs (root or any module) through `mavenRepo`'s virtual repo, which silently shadows any repo ID `mavenRepo` doesn't aggregate. Find the `<repositories>` entry the missing artifact's repo ID comes from and check its `<url>` host against this feature's `artifactoryUrl`:
- **Same host** → add the ID to `additionalMavenRepoIds` (comma-separated) in `devcontainer.json` to exclude it from the mirror so it resolves directly.
- **Different host** (e.g. `jitpack.io`, a legacy internal Artifactory, GitHub Packages) → do NOT add it to `additionalMavenRepoIds`. That option reuses this feature's real `ARTIFACTORY_EMAIL`/`ARTIFACTORY_TOKEN`, so adding a different-host ID would send those credentials to that other host on its first auth challenge — a credential leak, not a fix. That repo needs its own credential/resolution setup instead.