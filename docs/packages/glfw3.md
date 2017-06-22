DerelictGLFW3 provides both [static and dynamic bindings] to the [GLFW3] library. DerelictGLFW3 4.0.x supports GLFW3 version 3.2.x. Older versions of GLFW3 are supported by versions of DerelictGLFW3 lower than 4.0, but those DerelictGLFW3 versions use an older version of DerelictUtil that will be incompatible with the latest version of other Derelict packages you may use in your project (such as DerelictGL3). Keep this in mind if you need to use GLFW3 3.1 or lower in the dynamic binding configuration (this does not apply to the static binding).

The following sections show how to use DerelictGLFW3 in your own projects. See the pages under [Compiling and Linking] Derelict packages for more details.

### With DUB projects

The simplest way to use DerelictGLFW3 is to use [DUB] to manage your project. Projects configured via `dub.json` should add the package name and minimum desired version to the `dependencies` block:

```json
"dependencies": {
    "derelict-glfw3": "~>4.0.0-beta"
} 
```

Projects configured via `dub.sdl` should add the following line:

```bash
dependency "derelict-glfw3" version="~>4.0.0-beta"
```

The package is configured to use the dynamic binding by default. In order to enable static binding, add the `derelict-glfw3-static` subconfiguration:

**dub.json**
```json
"subConfigurations": {
    "derelict-sdl2": "derelict-glfw3-static"
}
```

**dub.sdl**
```bash
subConfiguration "derelict-sdl2" "derelict-glfw3-static"
```

Alternatively, the static binding can be enabled by adding either the `Derelict_Static` version or the `DerelictGLFW3_Static` version to your project configuration. The former will enable static binding for all Derelict packages in your project that support it. The latter will enable them only for DerelictGLFW3.

**dub.json**
```json
"versions": ["DerelictGLFW3_Static"]
```

**dub.sdl**
```bash
versions "DerelictGLFW3_Static"
```

Using the subconfiguration is the preferred approach, as it completely eliminates all unneeded modules from the build.

### With non-DUB projects

It is not required to use DUB to manage your project in order to use DerelictGLFW3, but DUB is the only supported option for building the DerelictGLFW3 library. To do so, first ensure that DUB, a D compiler, and git are all available on your system path. Then see the documentation on compiling any Derelict package [without DUB], substituting `derelict-glfw3` and `4.0.0-beta` where appropriate in the example commands.

### Loading

When DerelictGLFW3 is configured to produce a dynamic binding, the GLFW3 shared library must be loaded manually by calling the `DerelictGLFW3.load`. There is no link-time dependency on the GLFW3 library, only a runtime dependency. See the section further down on [obtaining the binaries] for information on how to obtain the GLFW3 runtime binaries.

The following code demonstrates how to load the GLFW3 library:

```d
import derelict.glfw3;
void main() {
    DerelictGLFW3.load();
}
```

### Linking

Configuring DerelictGLFW3 as a static binding eliminates the need to manually load the GLFW3 library at runtime. However, it introduces a link-time dependency and still has a runtime dependency when linking dynamically. See the page on [static and dynamic bindings] if the difference between static/dynamic bindings and static/dynamic linking is not clear to you.

To use the GLFW3 library with the static DerelictGLFW3 binding, it's necessary to install the appropriate development packages. [See below](#getting-the-libraries) for information on how to obtain the GLFW3 development binaries.

Once the development binaries are installed on your system, you can link to them by adding the appropriate library name to your config file. The following example shows how to link with GLFW3.

**dub.json**
```json
"libs": {
    "GLFW3"
}
```

**dub.sdl**
```bash
libs "GLFW3"
```

By default, this will link dynamically on non-Windows systems. Please see your system compiler documentation for information on how to link statically, though it's strongly recommended to link dynamically.

On Windows systems, the above may link statically or dynamically, depending on how the libraries are named and which is on the library path.

Linking statically will require linking with additional system libraries.

### Getting the libraries

Development and runtime binaries for Linux and *BSD distributions can be obtained through the system package manager, though they may not be the latest version. For the dynamic binding configuration, only the runtime package is needed. For the static binding configuration, the development package is required.

The latest binaries for Windows can be obtained at the [GLFW3 download page]. 

Windows binaries can also be obtained through [vcpkg] and [NuGet].

Mac OS X binaries can also be obtained through [Homebrew] and [MacPorts].

Finally, it possible on every platform to obtain the source for the and build the binaries manually on all supported platforms. The latest source release is available at the [GLFW3 download page]. All source releases and the current development version can be obtained through the [GLFW GitHub repository].


[GLFW3]: http://www.glfw.org/
[static and dynamic bindings]: ../bindings
[DUB]: https://code.dlang.org/getting_started
[Compiling and Linking]: ../building/overview
[without DUB]: ../building/without-dub
[vcpkg]: https://github.com/Microsoft/vcpkg
[NuGet]: https://www.nuget.org/
[Homebrew]: https://brew.sh/
[Macports]: https://www.macports.org/
[GLFW GitHub repository]: https://github.com/glfw/glfw