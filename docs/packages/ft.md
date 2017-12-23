DerelictFT provides both [static and dynamic bindings] to the [Freetype] library.

[Freetype]: https://www.freetype.org/
[static and dynamic bindings]: ../bindings

### Releases

The DUB package name of DerelictFT is `derelict-ft`.

The current DUB package version is `~>2.0.0-beta`.

The following table shows the correlation between DerelictFT releases (the most recent patch release of each `major.minor` series), its corresponding git branch, FreeType versions, and DerelictUtil versions. The latest release is listed at the top and is the recommended version. When using DerelictFT with other Derelict packages, please ensure all of the Derelict packages use the same DerelictUtil `major.minor` series.

| DerelictFT Version | git Branch     | FreeType Version | DerelictUtil Version | Supported |
| --------------------- | ----------     | ------------ | -------------------- | --------- |
| 2.0.0-beta            | [master]/[2.0] | 2.6.x - 2.7.x | 3.0.x                | &#x2714;  |
| 1.1.3                 | [2.6]          | 2.6.x        | 2.0.x                | &#x2714; (bugfix only)  |
| 1.0.2                 | [2.5]          | 2.5.x        | 1.0.x                | &#x2716;  |

All new development happens on the master branch. Pull requests and issues reported on unsupported branches will be ignored. DerelictFT does not support a static binding configuration.

[master]: https://github.com/DerelictOrg/DerelictFT/tree/master
[2.0]: https://github.com/DerelictOrg/DerelictFT/tree/2.0
[2.5]: https://github.com/DerelictOrg/DerelictFT/tree/2.6
[2.5]: https://github.com/DerelictOrg/DerelictFT/tree/2.5

### Using DerelictFT

See the generic documentation on [Compiling and Linking] for generic information on incorporating any Derelict package in your project.

[Compiling and Linking]: ../building/overview

#### Obtaining the FreeType binaries

Precompiled binaries for Windows can be downloaded from the [FreeType download page]. Binaries compiled with both Visual Studio and MinGW can be used with DerelictFT.

Windows binaries for Visual Studio are also available via [NuGet] and [vcpkg].

Binaries for Mac OS X can be obtained through [Homebrew] or [MacPorts].

Binaries for other systems can be obtained through the system package manager.

The FreeType sources can be downloaded from the [FreeType download page].

[FreeType download page]: https://www.freetype.org/download.html
[Homebrew]: http://brewformulas.org/Freetype
[Macports]: https://www.macports.org/ports.php?by=library&substr=freetype
[vcpkg]: https://github.com/Microsoft/vcpkg/tree/master/ports/freetype
[NuGet]: https://www.nuget.org/packages/freetype/

#### Loading the FreeType library

DerelictFT is a dynamic binding, so the functions from the FreeType library must be loaded at runtime via a call to the loader. As with most Derelict packages, the loader in DerelictFT is called `DerelictFT`. It can be loaded like so:

```d
import derelict.freetype;
// Alternatively:
// import derelict.freetype.ft;

void main() {
    DerelictFT.load();
}
```