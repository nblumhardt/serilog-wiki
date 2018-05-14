The Serilog Organisation generally uses the following structure for the projects regardless of function (sinks, enrichers etc.)

Projects are named via the following conventions:
* Sinks - `Serilog.Sinks.xx`
* Enrichers - `Serilog.Enricher.xx`

## Code Structure

Folders 
* `src` - Location of the source files
* `test` - Location of testing resources.  XUnit is the current testing framework.
* `assets` - Location of shared resources such as `.snk`, package logos.

## Builds

Serilog projects use [AppVeyor](https://ci.appveyor.com/project/serilog/serilog) for builds, with [Travis](https://travis-ci.org/serilog/serilog) used for cross platform builds.

* Builds are triggered on PRs via [GitHub WebHooks](https://developer.github.com/webhooks/)

### `build.ps1`
* A PowerShell build script is included in all projects that allows execution locally & on the AppVeyor build system.
* Version of the library is set via the [VersionPrefix](https://github.com/serilog/serilog/blob/dev/src/Serilog/Serilog.csproj#L5) and the 

### `.appveyor.yml`
AppVeyor build definitions are entirely configured via the `.yml` file.  Key areas of the AppVeyor setup include:
* Release configurations are used for builds
* Artifacts are named `Serilog.xxx.xxx.nupkg` (See [example](https://github.com/serilog/serilog/blob/dev/appveyor.yml#L9) with dev packages suffixed with `-dev-{build number}`
* Releases are deployed to NuGet on `master` & `dev` branch merges
* Releases are deployed to GitHub on `master` branch merges
* Builds are executed under there [Serilog Build](https://github.com/serilog-build) account.

### `.travis.yml`
* Setup uses [standard Travis images](https://github.com/serilog/serilog/blob/dev/.travis.yml#L2)

### `build.sh`
* Script executes builds and tests for relevant TFMs.