DerelictSDL2 provides both [static and dynamic bindings] the 2.x series of the [SDL (Simple Direct Media Layer)] library. DerelictSDL2 3.0.0 and higher supports all releases of SDL2 from version 2.0.0 to version 2.0.5 via Derelict's [SharedLibVersion] feature. The package also includes bindings to the following SDL satellite libraries:

* [SDL_image]
* [SDL_mixer]
* [SDL_net]
* [SDL_ttf]

[SDL (Simple Direct Media Layer)]: https://www.libsdl.org/
[static and dynamic bindings]: ../bindings
[SharedLibVersion]: ../loading/loader/#ShaderLibVersion

### Releases

The DUB package name of DerelictSDL2 is `derelict-sdl2`.

The current DUB package version is `~>3.0.0-beta`.

The following table shows the correlation between DerelictSDL2 releases (the most recent patch release of each `major.minor` series), its corresponding git branch, SDL versions, and DerelictUtil versions. The latest release is listed at the top and is the recommended version. When using DerelictSDL2 with other Derelict packages, please ensure all of the Derelict packages use the same DerelictUtil `major.minor` series.

| DerelictSDL2 Version  | git Branch     | SDL  Version | DerelictUtil Version | Supported |
| --------------------- | ----------     | ------------ | -------------------- | --------- |
| 3.0.0-beta.2          | [master]/[3.0] | 2.0.0 - 2.0.5| 3.0.x                | &#x2714;  |
| 2.1.4                 | [2.1]          | 2.0.0 - 2.0.4| 2.0.x                | &#x2714; (bugfix only)  |
| 2.0.2                 | [2.0.2]        | 2.0.0 - 2.0.4| 2.0.x                | &#x2716;  |
| 1.9.7                 | n/a            | 2.0.2 - 2.0.3| 2.0.x                | &#x2716;  |
| 1.2.16                | n/a            | 2.0.2 - 2.0.3| 1.0.x                | &#x2716;  |
| 1.1.17                | n/a            | 2.0.0        | 1.0.x                | &#x2716;  |

All new development happens on the master branch. Pull requests and issues reported on unsupported branches will be ignored. Only the 3.0.x series supports the static binding configuration. The 2.1.x series should be preferred only for compatibility with other Derelict packages that require DerelictUtil 2.0.x.

!!! note
    The satellite libraries are distributed separately from the main SDL packages and have their own versioning scheme. However, their APIs change very rarely, so they are excluded from the table above. The latest release of any of the satellite libraries should be compatible with any version of DerelictSDL2.

[master]: https://github.com/DerelictOrg/DerelictSDL2/tree/master
[3.0]: https://github.com/DerelictOrg/DerelictSDL2/tree/3.0
[2.1]: https://github.com/DerelictOrg/DerelictSDL2/tree/2.1
[2.0.2] https://github.com/DerelictOrg/DerelictSDL2/tree/2.0.2

### Using DerelictSDL2

See the generic documentation on [Compiling and Linuking] for generic information on incorporating any Derelict package in your project.

[Compiling and Linking]: ../building/overview

#### Obtaining the SDL binaries

Precompiled binaries for Windows and Mac OS X can be downloaded from the the following pages:

* [SDL2]
* [SDL_image]
* [SDL_mixer]
* [SDL_net]
* [SDL_ttf]

For the dynamic binding configuration, either the Runtime Binaries or the Development Libraries will suffice. They all contain the shared libraries. For the static binding configuration, the Development Libraries should be used, the Visual C++ version on Windows (see the page on [Windows-specific compilation] for more information).

Windows binaries for Visual Studio are also available via [NuGet] and [vcpkg].

Binaries Mac OS X can be obtained through [Homebrew] or [MacPorts].

Binaries for other systems can be obtained through the system package manager.

The SDL source can be downloaded from the download page or cloned from the [SDL mercurial repository].

[SDL2]: https://www.libsdl.org/download-2.0.php
[SDL_image]: https://www.libsdl.org/projects/SDL_image/
[SDL_mixer]: https://www.libsdl.org/projects/SDL_mixer/
[SDL_net]: https://www.libsdl.org/projects/SDL_net/
[SDL_ttf]: https://www.libsdl.org/projects/SDL_ttf/

[Windows-specific compilation]: ../building/windows
[NuGet]: https://www.nuget.org/
[vcpkg]: https://github.com/Microsoft/vcpkg
[Homebrew]: https://brew.sh/
[Macports]: https://www.macports.org/
[SDL mercurial repository]: https://libsdl.org/hg.php

#### Choosing the configuration

By default, DerelictSDL2 will be configured as a dynamic binding. There are two ways to enable the static binding configuration. The recommended way is to add a `subConfiguration` entry to your project configuration with the value `derelict-sdl2-static` and the appropriate library, as in the following examples. 

**dub.json**
```json
"dependencies": {
    "derelict-sdl2": "~>3.0.0-beta"
},
"subConfigurations": {
    "derelict-sdl2": "derelict-sdl2-static"
},
"libs": {
    ["sdl2"]
},
```

**dub.sdl**
```bash
dependency "derelict-sdl2" "~>3.0.0-beta"
subConfiguration "derelict-sdl2" "derelict-sdl2-static"
libs "sdl2"
```

The alternative is to replace the `subConfiguration` with a `versions` entry and give it one of two values, either `Derelict_Static` or `DerelictSDL2_Static`. The former will enable the static binding configuration of any other Derelict packages in your project that support it. The latter will enable it only for DerelictSDL2.

**dub.json**
```json
"dependencies": {
    "derelict-sdl2": "~>3.0.0-beta"
},
"versions": ["DerelictSDL2_Static"],
"libs": {
    ["sdl2"]
}
```

**dub.sdl**
```bash
dependency "derelict-sdl2" "~>3.0.0-beta"
versions "DerelictSDL2_Static"
libs "sdl2dll" platform="windows"
libs "sdl2" platform="posix"
```

#### Loading the SDL libraries

When using the static binding configuration, you must link with either the static or dynamic libraries at link-time. In that case, all SDL functions can be called directly and nothing special need be done at runtime to use them. 

When using the dynamic binding configuration, the functions must be loaded at runtime via a call to the appropriate loader, such as `DerelictSDL2.load`, as shown in the following example.

```d
import derelict.sdl2.sdl;
void main() {
    DerelictSDL2.load();
}
```

Each of the satellite libraries has its own module and its own loader:

```d
import derelict.sdl2.image,
       derelict.sdl2.sdl;
void main() {
    DerelictSDL2.load();
    DerelictSDL2Image.load();
}
```

All of the loaders and bindings in the DerelictSDL2 package can be imported at once via `import derelict.sdl2`.

#### SharedLibVersion

Since version `2.0.2`, the DerelictSDL2 loader (but none of the satellite loaders) has supported loading the shared library with the `SharedLibVersion` structure (see the table at the top of the page to see which versions are available in each release). By default, the loader attempts to load the highest supported version of SDL and will fail if that version is unavailable. By using `SharedLibVersion`, you can specify a minimum required version of SDL. All higher versions will still load, but all lower versions will fail. For example:

```d
import derelict.sdl2.sdl;
import derelict.util.loader : SharedLibVersion;

void main() {
    DerelictSDL2.load(SharedLibVersion(2, 0, 2));
}
```

!!! note 
    If you want to use SDL 2.0.1 as a minimum version to take advantage of the [file system API] that was not available in 2.0.0, then be aware that there was a bug in version 2.0.1 on Windows that prevented the preference path from being created on the user's system. The bug was fixed in SDL 2.0.2. In this case, the following code will do the right thing:

```d
import derelict.sdl2.sdl;
import derelict.util.loader : SharedLibVersion;

void main() {
    // Require 2.0.2 on Windows because of the pref path bug in 2.0.1
    version(Windows) DerelictSDL2.load(SharedLibVersion(2, 0, 2));
    else DerelictSDL2.load(SharedLibVersion(2, 0, 1));
}
```

See the loader documentation for more info.

[loader]: ../loading/loader