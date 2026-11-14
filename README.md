# mod-workflows

Reusable GitHub Actions workflows for my Stonecutter-based Fabric mods.

## Versioning

Callers pin `@v1`. Each change gets a new `v1.x.y` tag and `v1` is moved to it. Exact `v1.x.y` tags are protected and never move.

## Requirements

- Stonecutter project with one subproject per Minecraft version under `versions/<v>`
- Jars named `<name>-<version>+<mc>.jar`, where `<version>` matches the release tag
- `CHANGELOG.md` with the newest release as its first `## ` section
- A `publishMods` task (me.modmuss50.mod-publish-plugin) that reads `MODRINTH_TOKEN`, `CURSEFORGE_TOKEN` and `CHANGELOG` from the environment

## Build

```yaml
name: Build

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build:
    uses: ZephByte/mod-workflows/.github/workflows/build.yml@v1
```

## Release

Push a tag matching the mod version in `gradle.properties`, e.g. `v2.0.0`. This creates one GitHub Release with a jar per Minecraft version and runs `publishMods`.

```yaml
name: Release

on:
  push:
    tags:
      - 'v*'

jobs:
  release:
    permissions:
      contents: write
    uses: ZephByte/mod-workflows/.github/workflows/release.yml@v1
    with:
      release-name: My Mod
    secrets: inherit
```

| Input | Default | |
|---|---|---|
| `release-name` | required | GitHub Release title prefix |
| `version-property` | `mod_version` | `gradle.properties` key the tag must match |
| `java-version` | `25` | |
| `sync-modrinth-description` | `true` | Replace the Modrinth project description with `README.md`, with relative links pointed at the tag on GitHub. Needs `MODRINTH_TOKEN` to have the Write projects scope |
