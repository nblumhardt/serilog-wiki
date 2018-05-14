The Serilog Organisation generally uses the following structure for the projects regardless of function (sinks, enrichers etc.)

## Code Structure

Folders 
* `src` - Location of the source files
* `test` - Location of testing resources
* `assets` - Location of shared resources such as `.snk`, package logos.

## Builds

Serilog projects use [AppVeyor](https://ci.appveyor.com/project/serilog/serilog) for builds, with [Travis](https://travis-ci.org/serilog/serilog) used for cross platform builds.

### `build.ps1`
* A PowerShell build script is included in all projects that allows execution locally & on the AppVeyor build system.

### `.appveyor.yml`
AppVeyor build definitions are entirely configured via the `.yml` file.  Key areas of the AppVeyor setup include:
* Release configurations are used for builds
* Artifacts are named `Serilog.xxx.xxx.nupkg` (See [example](https://github.com/serilog/serilog/blob/dev/appveyor.yml#L9)
* Releases are deployed to NuGet on `master` & `dev` branch merges
* Releases are deployed to GitHub on `master` branch merges

### `.travis.yml`
* Setup uses [standard Travis images](https://github.com/serilog/serilog/blob/dev/.travis.yml#L2)

### `build.sh`



## Travis
