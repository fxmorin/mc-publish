## Publish Minecraft Mods - GitHub Action

[![GitHub tag](https://img.shields.io/github/tag/Kir-Antipov/mc-publish.svg?cacheSeconds=3600)](https://github.com/Kir-Antipov/mc-publish/releases/latest)
[![GitHub build status](https://img.shields.io/github/workflow/status/Kir-Antipov/mc-publish/ci/master?cacheSeconds=3600)](https://github.com/Kir-Antipov/mc-publish/actions/workflows/ci.yml)
[![GitHub license](https://img.shields.io/github/license/Kir-Antipov/mc-publish.svg?cacheSeconds=36000)](https://github.com/Kir-Antipov/mc-publish#readme)

The `Publish Minecraft Mods` action helps you upload assets of your Minecraft mods to GitHub Releases, Modrinth and CurseForge. This is a cross-platform action that runs on any environment.

### Usage

```yml
jobs:
  build:
    # ...
    steps:
      - uses: Kir-Antipov/mc-publish@v2.1
        with:
          modrinth-id: AANobbMI
          modrinth-token: ${{ secrets.MODRINTH_TOKEN }}

          curseforge-id: 394468
          curseforge-token: ${{ secrets.CURSEFORGE_TOKEN }}

          github-tag: mc1.17.1-0.3.2
          github-token: ${{ secrets.GITHUB_TOKEN }}

          files-primary: build/libs/!(*-@(dev|sources)).jar
          files-secondary: build/libs/*-@(dev|sources).jar

          name: Sodium 0.3.2 for Minecraft 1.17.1
          version: mc1.17.1-0.3.2
          version-type: release
          changelog-file: CHANGELOG.md

          loaders: |
            fabric
            forge
          game-versions: |
            1.16.3
            1.17
            1.17.1
            21w37a
          dependencies: |
            required-dependency | depends | *
            required-dependency | depends
            required-dependency
            optional-dependency | recommends | 0.1.0
            suggested-dependency | suggests | 0.2.0
            included-dependency | includes | 0.3.0
            conflicting-dependency | conflicts | *
            incompatible-dependency | breaks | *
          java: |
            8
            16

          retry-attempts: 2
          retry-delay: 10000
```

### Minimalistic Example

Most of the values are automatically resolved (read further for more info). Therefore, here's all you really need to publish your mod:

```yml
jobs:
  build:
    # ...
    steps:
      - uses: Kir-Antipov/mc-publish@v2.1
        with:
          # You don't need this section if you don't want to publish
          # your assets to Modrinth
          modrinth-id: AANobbMI
          modrinth-token: ${{ secrets.MODRINTH_TOKEN }}

          # You don't need this section if you don't want to publish
          # your assets to CurseForge
          curseforge-id: 394468
          curseforge-token: ${{ secrets.CURSEFORGE_TOKEN }}

          # You don't need this section if you don't want to publish
          # your assets to GitHub
          github-token: ${{ secrets.GITHUB_TOKEN }}
```

### Inputs

| Name | Description | Default value | Examples |
|------|-------------|---------------|----------|
| [modrinth-id](#user-content-modrinth-id) | The ID of the Modrinth project to upload to | A value specified in the config file | `AANobbMI` |
| [modrinth-token](#user-content-modrinth-token) | A valid token for the Modrinth API | ❌ | `${{ secrets.MODRINTH_TOKEN }}` |
| [curseforge-id](#user-content-curseforge-id) | The ID of the CurseForge project to upload to | A value specified in the config file | `394468` |
| [curseforge-token](#user-content-curseforge-token) | A valid token for the CurseForge API | ❌ | `${{ secrets.CURSEFORGE_TOKEN }}` |
| [github-tag](#user-content-github-tag) | The tag name of the release to upload assets to | A tag of the release that triggered the action | `mc1.17.1-0.3.2` |
| [github-token](#user-content-github-token) | A valid token for the GitHub API | ❌ | `${{ secrets.GITHUB_TOKEN }}` |
| [files](#user-content-files) | A glob of the file(s) to upload | ❌ | `build/libs/*.jar` |
| [files-primary](#user-content-files-primary) | A glob of the primary files to upload | `build/libs/!(*-@(dev\|sources)).jar` | `build/libs/!(*-@(dev\|sources)).jar` |
| [files-secondary](#user-content-files-secondary) | A glob of the secondary files to upload | `build/libs/*-@(dev\|sources).jar` | `build/libs/*-@(dev\|sources).jar` |
| [name](#user-content-name) | The name of the version | A title of the release that triggered the action | `Sodium 0.3.2 for Minecraft 1.17.1` |
| [version](#user-content-version) | The version number | A tag of the release that triggered the action | `mc1.17.1-0.3.2` |
| [version-type](#user-content-version-type) | The type of the release | Will be parsed from the [`version`](#user-content-version) value | `alpha` <br> `beta` <br> `release` |
| [changelog](#user-content-changelog) | The changelog for this version | A body of the release that triggered the action | `This release fixes a few more issues in Sodium 0.3 for Minecraft 1.17.1.` |
| [changelog-file](#user-content-changelog-file) | A glob of the changelog file | ❌ | `CHANGELOG.md` |
| [loaders](#user-content-loaders) | A list of supported mod loaders | `fabric` for Fabric mods <br> `forge` for Forge mods | `fabric` <br> `forge` <br> `rift` |
| [game-versions](#user-content-game-versions) | A list of supported Minecraft versions | A value specified in the config file, if any; otherwise, it will be parsed from the [`version`](#user-content-version) value | `21w37a` <br> `1.17` |
| [version-resolver](#user-content-version-resolver) | Determines the way automatic [`game-versions`](#user-content-game-versions) resolvement works | `releasesIfAny` | `exact` <br> `latest` <br> `all` <br> `releases` <br> `releasesIfAny` |
| [dependencies](#user-content-dependencies) | A list of dependencies | A dependency list specified in the config file  | `fabric \| depends \| 0.40.0` <br> `fabric-api` |
| [java](#user-content-java) | A list of supported Java versions | *empty string* | `Java 8` <br> `Java 1.8` <br> `8` |
| [retry-attempts](#user-content-retry-attempts) | The maximum number of attempts to publish assets | `2` | `2` <br> `10` <br> `-1` |
| [retry-delay](#user-content-retry-delay) | Time delay between attempts to publish assets (in milliseconds) | `10000` | `10000` <br> `60000` <br> `0` |

#### modrinth-id

The ID of the Modrinth project to upload to.

```yaml
modrinth-id: AANobbMI
```

Can be automatically retrieved from the config file of your mod:

- `fabric.mod.json` (Fabric)

  - Custom `mc-publish` field *(recommended)*:
      ```json
      {
        // ...
        "custom": {
          "mc-publish": {
            "modrinth": "AANobbMI"
          }
        },
      }
      ```

  - Custom [`modmanager`](https://github.com/DeathsGun/ModManager) field *(recommended)*:
      ```json
      {
        // ...
        "custom": {
          "modmanager": {
            "modrinth": "AANobbMI"
          }
        },
      }
      ```

  - Custom `projects` field:
      ```json
      {
        // ...
        "custom": {
          "projects": {
            "modrinth": "AANobbMI"
          }
        },
      }
      ```

  - `projects` field:
      ```json
      {
        // ...
        "projects": {
          "modrinth": "AANobbMI"
        },
      }
      ```

- `mods.toml` (Forge)

  - Custom `mc-publish` field *(recommended)*:
      ```toml
      [custom.mc-publish]
          modrinth="AANobbMI"
      ```

  - Custom `projects` field:
      ```toml
      [custom.projects]
          modrinth="AANobbMI"
      ```

  - `projects` field:
      ```toml
      [projects]
          modrinth="AANobbMI"
      ```

#### modrinth-token

A valid token for the Modrinth API. It's required if you want to publish your assets to Modrinth.

```yaml
modrinth-token: ${{ secrets.MODRINTH_TOKEN }}
```

#### curseforge-id

The ID of the CurseForge project to upload to.

```yaml
curseforge-id: 394468
```

Can be automatically retrieved from the config file of your mod:

- `fabric.mod.json` (Fabric)

  - Custom `mc-publish` field *(recommended)*:
      ```json
      {
        // ...
        "custom": {
          "mc-publish": {
            "curseforge": 394468
          }
        },
      }
      ```

  - Custom [`modmanager`](https://github.com/DeathsGun/ModManager) field *(recommended)*:
      ```json
      {
        // ...
        "custom": {
          "modmanager": {
            "curseforge": 394468
          }
        },
      }
      ```

  - Custom `projects` field:
      ```json
      {
        // ...
        "custom": {
          "projects": {
            "curseforge": 394468
          }
        },
      }
      ```

  - `projects` field:
      ```json
      {
        // ...
        "projects": {
          "curseforge": 394468
        },
      }
      ```

- `mods.toml` (Forge)

  - Custom `mc-publish` field *(recommended)*:
      ```toml
      [custom.mc-publish]
          curseforge=394468
      ```

  - Custom `projects` field:
      ```toml
      [custom.projects]
          curseforge=394468
      ```

  - `projects` field:
      ```toml
      [projects]
          curseforge=394468
      ```

#### curseforge-token

A valid token for the CurseForge API. It's required if you want to publish your assets to CurseForge.

```yaml
curseforge-token: ${{ secrets.CURSEFORGE_TOKEN }}
```

#### github-tag

The tag name of the release to upload assets to. If no value is provided, a tag of the release that triggered the action will be used.

```yaml
github-tag: mc1.17.1-0.3.2
```

#### github-token

A valid token for the GitHub API. It's required if you want to publish your assets to GitHub.

```yaml
github-token: ${{ secrets.GITHUB_TOKEN }}
```

#### files

A glob of the file(s) to upload. If you want to publish multiple files, it's recommended to use [`files-primary`](#user-content-files-primary) and [`files-secondary`](#user-content-files-secondary) instead.

```yaml
files: build/libs/*.jar
```

#### files-primary

A glob of the primary files to upload. Default value is `build/libs/!(*-@(dev|sources)).jar`.

```yaml
files-primary: build/libs/!(*-@(dev|sources)).jar
```

#### files-secondary

A glob of the secondary files to upload. Default value is `build/libs/*-@(dev|sources).jar`.

```yaml
files-secondary: build/libs/*-@(dev|sources).jar
```

#### name

The name of the version. If no value is provided, a title of the release that triggered the action will be used. If you want Modrinth and CurseForge to determine the version name themselves, omit the field with an empty string (`""`).

```yaml
name: Sodium 0.3.2 for Minecraft 1.17.1
```

#### version

The version number. If no value is provided, a tag of the release that triggered the action is used.

```yaml
version: mc1.17.1-0.3.2
```

#### version-type

The type of the release. If no value is provided, it will be parsed from the [`version`](#user-content-version) value (e.g., `0.40.0+1.17-alpha` results in `alpha`).

```yaml
version-type: release
```

#### changelog

The changelog for this version. If no value is provided, a body of the release that triggered the action will be used.

```yaml
changelog: This release fixes a few more issues in Sodium 0.3 for Minecraft 1.17.1.
```

#### changelog-file

A glob of the changelog file.

```yaml
changelog-file: CHANGELOG.md
```

#### loaders

A list of supported mod loaders. If no value is provided, `fabric` will be used for valid Fabric mods and `forge` will be used for valid Forge mods.

```yaml
loaders: |
  fabric
  forge
```

#### game-versions

A list of supported Minecraft versions. If no value is provided, the minimum supported version will be retrieved from the configuration file of your mod, if any, otherwise it will be parsed from the [`version`](#user-content-version) value (e.g., `0.40.0+1.17` results in `1.17`), and then it will be expanded using the specified [`version-resolver`](#user-content-version-resolver).

```yaml
game-versions: |
  1.16.5
  1.17
  1.17.1
  21w37a
```

#### version-resolver

Determines the way automatic `game-versions` resolvement works. Default value is `releasesIfAny`.

```yaml
version-resolver: latest
```

Available values:

 - `exact` - exact game version *(`1.16` -> `1.16`)*
 - `latest` - the latest release of the given minor *(`1.16` -> `1.16.5`)*
 - `all` - all versions of the given minor starting with the specified build *(`1.16.5` -> `[20w45a, 20w46a, 20w48a, 20w49a, 20w51a, 1.16.5-rc1, 1.16.5]`)*
 - `releases` - all releases of the given minor starting with the specified build *(`1.16.3` -> `[1.16.3, 1.16.4, 1.16.5]`)*
 - `releasesIfAny` - all releases of the given minor starting with the specified build, if any; otherwise, all versions

#### dependencies

A list of dependencies.

```yaml
dependencies: |
  required-dependency | depends | *
  required-dependency | depends
  required-dependency
  optional-dependency | recommends | 0.1.0
  suggested-dependency | suggests | 0.2.0
  included-dependency | includes | 0.3.0
  conflicting-dependency | conflicts | *
  incompatible-dependency | breaks | *
```

As you can see, each dependency should be written on a new line using the following format - `{id} | {kind=depends} | {version=*}`.

Available dependency kinds:
 - `depends` - for dependencies required to run. Without them a game will crash.
 - `recommends` - for dependencies not required to run. Without them a game will log a warning.
 - `suggests` - for dependencies not required to run. Can be used as a kind of metadata.
 - `includes` - for dependencies embedded into the mod. Can be used as a kind of metadata.
 - `conflicts` - for mods whose together with yours cause some kind of bugs, etc. With them a game will log a warning.
 - `breaks` - for mods whose together with yours might cause a game crash. With them a game will crash.

Can be automatically retrieved from the config file of your mod:

- `fabric.mod.json` (Fabric)
  ```json
  "depends": {
    "required-dependency": "*"
  },
  "recommends": {
    "optional-dependency": "0.1.0"
  },
  "suggests": {
    "suggested-dependency": "0.2.0"
  },
  "includes": {
    // Check if your version of the Fabric loader supports objects in dependency declarations.
    "included-dependency": {
      "version": "0.3.0",
      "custom": {
        "mc-publish": {
          "ignore": false, // `mc-publish` will ignore this dependency, if `ignore` is set to true
          "modrinth": "included-dependency-fabric", // Modrinth's project slug
          "curseforge": "included-dependency-fabric" // CurseForge's project slug
        }
      }
    }
  },
  "conflicts": {
    "conflicting-dependency": "*"
  },
  "breaks": {
    "incompatible-dependency": "*"
  },
  ```

- `mods.toml` (Forge)
  ```toml
  [[dependencies.mod-id]]
    modId="required-dependency"
    mandatory=true
    versionRange="*"
    ordering="NONE"
    side="BOTH"

  [[dependencies.mod-id]]
    modId="optional-dependency"
    mandatory=false
    versionRange="0.1.0"
    ordering="NONE"
    side="BOTH"

  [[dependencies.mod-id]]
    modId="included-dependency"
    mandatory=false
    embedded=true
    versionRange="0.3.0"
    ordering="NONE"
    side="BOTH"
    [dependencies.mod-id.custom.mc-publish]
        ignore=false # `mc-publish` will ignore this dependency, if `ignore` is set to true
        modrinth="included-dependency-forge" # Modrinth's project slug
        curseforge="included-dependency-forge" # CurseForge's project slug

  [[dependencies.mod-id]]
    modId="incompatible-dependency"
    mandatory=false
    incompatible=true
    versionRange="*"
    ordering="NONE"
    side="BOTH"
  ```

#### java

A list of supported Java versions. It's omitted by default.

```yaml
java: |
  8
  16
  Java 17
```

#### retry-attempts

The maximum number of attempts to publish assets.

```yaml
retry-attempts: 2
```

#### retry-delay

Time delay between attempts to publish assets (in milliseconds).

```yaml
retry-delay: 10000
```