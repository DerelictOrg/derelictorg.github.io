DerelictGLFW3 provides both [static and dynamic bindings] to the [GLFW] library.

[GLFW]: http://www.glfw.org/
[static and dynamic bindings]: ../bindings

### Releases

The DUB package name of DerelictGLFW3 is `derelict-glfw3`.

The following table shows the correlation between DerelictGLFW3 releases (the most recent patch release of each `major.minor` series), its corresponding git branch, GLFW versions, and DerelictUtil versions. The latest release is listed at the top and is the recommended version. When using DerelictGLFW3 with other Derelict packages, please ensure all of the Derelict packages use the same DerelictUtil `major.minor` series.

| DerelictGLFW3 Version | git Branch     | GLFW Version | DerelictUtil Version | Supported |
| --------------------- | ----------     | ------------ | -------------------- | --------- |
| 4.0.0-beta.1          | [master]/[4.0] | 3.2.x        | 3.0.x                | &#x2714;  |
| 3.1.3                 | [3.2]          | 3.2.x        | 2.0.x                | &#x2714; (bugfix only)  |
| 3.0.1                 | [3.1]          | 3.2.x        | 2.0.x                | &#x2716;  |
| 2.0.0                 | [3.0]          | 3.1.x        | 2.0.x                | &#x2716;  |
| 1.1.1                 | n/a            | 3.1.x        | 1.0.x                | &#x2716;  |
| 1.0.2                 | n/a            | 3.0.x        | 1.0.x                | &#x2716;  |

All new development happens on the master branch. Pull requests and issues reported on unsupported branches will be ignored. Both the 4.0.x series and the 3.1.x series support the static binding configuration, but the 3.1.x series should be preferred only for compatibility with other Derelict packages that require DerelictUtil 2.0.x.

[master]: https://github.com/DerelictOrg/DerelictGLFW3/tree/master
[4.0]: https://github.com/DerelictOrg/DerelictGLFW3/tree/4.0
[3.2]: https://github.com/DerelictOrg/DerelictGLFW3/tree/3.2
[3.1]: https://github.com/DerelictOrg/DerelictGLFW3/tree/3.1
[3.0]: https://github.com/DerelictOrg/DerelictGLFW3/tree/3.0

### Using DerelictGLFW3

See the generic documentation on [Compiling and Linuking] for generic information on incorporating any Derelict package in your project.

[Compiling and Linking]: ../building/overview

#### Obtaining the GLFW binaries

Precompiled binaries for Windows can be downloaded from the [GLFW download page]. The zip archive contains binaries for MinGW, and Visual Studio 2012, 2013, and 2015. When using the dynamic binding configuration, any of the DLLs from this archive can be loaded at runtime. When using the static binding configuration, only the Visual Studio binaries should be used. To link statically, use `glfw3.lib`. To link dynamically, use `glfw3dll.lib` and distribute the corresponding DLL with your executable. See the documentation on [Windows-specific compilation] for more details.

Windows binaries for Visual Studio are also available via [NuGet] and [vcpkg].

Binaries Mac OS X can be obtained through [Homebrew] or [MacPorts], though they may not always be the latest release.

Binaries for other systems can be obtained through the system package manager.

The GLFW source can be downloaded from the download page or cloned from the [GLFW GitHub repository].

[GLFW download page]: http://www.glfw.org/download.html
[Windows-specific compilation]: ../building/windows
[Homebrew]: https://brew.sh/
[Macports]: https://www.macports.org/
[GLFW GitHub repository]: https://github.com/glfw/glfw
[vcpkg]: https://github.com/Microsoft/vcpkg
[NuGet]: https://www.nuget.org/

#### Choosing the configuration

By default, DerelictGLFW3 will be configured as a dynamic binding. There are two ways to enable the static binding configuration. The recommended way is to add a `subConfiguration` entry to your project configuration with the value `derelict-glfw3-static` and the appropriate library, as in the following examples. Note that the example assumes linking dynamically, using the precompiled binaries from the GLFW website on Windows. In other binary distributions on Windows, the dynamic import library may simply be named `glfw3.lib` instead. In that case, a single `libs` entry with no platform suffix will suffice.  

**dub.json**
```json
"dependencies": {
    "derelict-glfw3": "~>4.0.0-beta"
},
"subConfigurations": {
    "derelict-glfw3": "derelict-glfw3-static"
},
"libs-windows": {
    "glfw3dll"
},
"libs-posix": {
    "glfw3"
}
```

**dub.sdl**
```bash
dependency "derelict-glfw3" "~>4.0.0-beta"
subConfiguration "derelict-glfw3" "derelict-glfw3-static"
libs "glfw3dll" platform="windows"
libs "glfw3" platform="posix"
```

The alternative is to replace the `subConfiguration` with a `versions` entry and give it one of two values, either `Derelict_Static` or `DerelictGLFW3_Static`. The former will enable the static binding configuration of any other Derelict packages in your project that support it. The latter will enable it only for DerelictGLFW3.

**dub.json**
```json
"dependencies": {
    "derelict-glfw3": "~>4.0.0-beta"
},
"versions": ["DerelictGLFW3_Static"],
"libs-windows": {
    "glfw3dll"
},
"libs-posix": {
    "glfw3"
}
```

**dub.sdl**
```bash
dependency "derelict-glfw3" "~>4.0.0-beta"
versions "DerelictGLFW3_Static"
libs "glfw3dll" platform="windows"
libs "glfw3" platform="posix"
```

#### Loading the GLFW library

When using the static binding configuration, you must link with either the static or dynamic library at link-time. In that case, all GLFW functions can be called directly and nothing special need be done at runtime to use them. 

When using the dynamic binding configuration, the functions must be loaded at runtime via a call to `DerelictGLFW3.load`, as shown in the following example.

```d
import derelict.glfw3;
void main() {
    DerelictGLFW3.load();
}
```