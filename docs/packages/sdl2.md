DerelictSDL2 provides both [static and dynamic bindings] the 2.x series of the [SDL (Simple Direct Media Layer)] library. DerelictSDL2 3.0.0 and higher supports all releases of SDL2 from version 2.0.0 to version 2.0.5 via Derelict's [SharedLibVersion] feature. The package also includes bindings to the following SDL satellite libraries:

* [SDL_image]
* [SDL_mixer]
* [SDL_net]
* [SDL_ttf]

The following sections show how to use DerelictSDL2 in your own projects. See the pages under [Compiling and Linking] Derelict packages for more details.

### With DUB projects

The simplest way to use DerelictSDL2 is to use [DUB] to manage your project. Projects configured via `dub.json` should add the package name and minimum desired version to the `dependencies` block:

```json
"dependencies": {
    "derelict-sdl2": "~>3.0.0-beta"
} 
```

Projects configured via `dub.sdl` should add the following line:

```bash
dependency "derelict-sdl2" version="~>3.0.0-beta"
```

The package is configured to use dynamic bindings by default. In order to enable static binding, add the `derelict-sdl2-static` subconfiguration:

**dub.json**
```json
"subConfigurations": {
    "derelict-sdl2": "derelict-sdl2-static"
}
```

**dub.sdl**
```bash
subConfiguration "derelict-sdl2" "derelict-sdl2-static"
```

Alternatively, the static bindings can be enabled by adding either the `Derelict_Static` version or the `DerelictSDL2_Static` version to your project configuration. The former will enable static binding for all Derelict packages in your project that support it. The latter will enable them only for DerelictSDL2, including the bindings to the SDL satellite libraries.

**dub.json**
```json
"versions": ["DerelictSDL2_Static"]
```

**dub.sdl**
```bash
versions "DerelictSDL2_Static"
```

Using the subconfiguration is the preferred approach, as it completely eliminates all unneeded modules from the build.

### With non-DUB projects

It is not required to use DUB to manage your project in order to use DerelictSDL2, but DUB is the only supported option for building the DerelictSDL2 library. To do so, first ensure that DUB, a D compiler, and git are all available on your system path. Then see the documentation on compiling any Derelict package [without DUB], substituting `derelict-sdl2` and `3.0.0-beta` where appropriate in the example commands.

### Loading

When DerelictSDL2 is configured to produce dynamic bindings, the SDL shared libraries must be loaded manually by calling the `load` method on the appropriate loader instance. There is no link-time dependency on the SDL libraries, only a runtime dependency. See the section further down on [obtaining the libraries]  for information on how to obtaint he SDL runtime binaries. 

The following code demonstrates how to load the SDL2 library:

```d
import derelict.sdl2.sdl;
void main() {
    DerelictSDL2.load();
}
```

Each satellite library must be loaded independently:

```d
import derelict.sdl2.image,
       derelict.sdl2.sdl;

void main() {
    DerelictSDL2.load();
    DerelictSDLImage.load();
}
```

The following loads all of the supported libraries:

```d
// Import all of the loaders, types and functions at once
import derelict.sdl2;

/* Alternatively:
import derelict.sdl2.image,
       derelict.sdl2.mixer,
       derelict.sdl2.net,
       derelict.sdl2.sdl,
       derelict.sdl2.ttf;
*/

void main() {
    DerelictSDL2.load();
    DerelictSDL2Image.load();
    DerelictSDL2Mixer.load();
    DerelictSDL2Net.load();
    DerelictSDL2TTF.load();
}
```

DerelictSDL2 also supports the `SharedLibVersion` feature for the SDL library, but not for the satellite libraries. By default, the loader attempts to load the highest supported version of SDL and will fail if it that version is unavailable. By using `SharedLibVersion`, you can specify a minimum required version of SDL. All higher versions will still load, but all lower versions will fail. For example:

```d
import derelict.sdl2.sdl;
import derelict.util.loader : SharedLibVersion;

void main() {
    DerelictSDL2.load(SharedLibVersion(2, 0, 2));
}
```

**Note:** If you want to use SDL 2.0.1 as a minimum version to take advantage of the [file system API] that was not available in 2.0.0, then be aware that there was a bug in version 2.0.1 on Windows that prevented the preference path from being created on the user's system. The bug was fixed in SDL 2.0.2. In this case, the following code will do the right thing:

```d
import derelict.sdl2.sdl;
import derelict.util.loader : SharedLibVersion;

void main() {
    // Require 2.0.2 on Windows because of the pref path bug in 2.0.1
    version(Windows) DerelictSDL2.load(SharedLibVersion(2, 0, 2));
    else DerelictSDL2.load(SharedLibVersion(2, 0, 1));
}
```

### Linking

Configuring DerelictSDL2 as a static binding eliminates the need to manually load any of the SDL libraries at runtime. However, it introduces a link-time dependency and still has a runtime dependency when linking dynamically. See the page on [static and dynamic bindings] if the difference between static/dynamic bindings and static/dynamic linking is not clear to you.

To use the SDL libraries with the static DerelictSDL2 binding, it's necessary to install the appropriate development packages. [See below](#getting-the-libraries) for information on how to obtain the SDL2 development binaries.

Once the development binaries are installed on your system, you can link to them by adding the appropriate library names to your config file. The following example shows how to link with SDL2 and SDL2_image.

**dub.json**
```json
"libs": {
    "SDL2",
    "SDL2_image"
}
```

**dub.sdl**
```bash
libs "SDL2" "SDL2_image"
```

By default, this will link dynamically on non-Windows systems. Please see your system compiler documentation for information on how to link statically, though it's strongly recommended to link dynamically.

On Windows sytems, the above may link statically or dynamically, depending on how the libraries are named and which are on the library path.

Linking statically will require linking with additional system libraries.

### Getting the libraries

Development and runtime binaries for Linux and *BSD distributions can be obtained through the system package manager. Binaries for Windows and Mac OS X can be obtained at the following links:

* [SDL2]
* [SDL_image]
* [SDL_mixer]
* [SDL_net]
* [SDL_ttf]

For the dynamic binding configuration, only the runtime binaries are required. For the static binding configuration, the development binaries are required (the develpoment packages include the runtime binaries).

For Windows, the `*-VC.zip` package should be downloaded for both DMD and LDC. This includes both the DLL and the import library, but no static library. When using DMD, this will require compiling your project with `-m32mscoff` or `-m64`, which uses the Microsoft linker and the Windows SDK libraries. Using vanilla DMD requires converting the format of the import libraries from COFF to OMF, which is beyond the scope of this documentation.

Windows binaries can also be obtained through [vcpkg], but this includes only the DLL and import library. No static libraries are installed.

Mac OS X binaries can also be obtained through [Homebrew] and [MacPorts].

Finally, it possible on every platform to obtain the source for the SDL2 libraries and build the binaries manually on all supported platforms. The latest source releases are avaliable at the links above. All the latest source releases and the current development version can be obtained through the [SDL mercurial repository].


[SDL (Simple Direct Media Layer)]: https://www.libsdl.org/
[SharedLibVersion]: ../loading/loader/#ShaderLibVersion
[static and dynamic bindings]: ../bindings
[DUB]: https://code.dlang.org/getting_started
[Compiling and Linking]: ../building/overview
[without DUB]: ../building/without-dub
[file system API]: https://wiki.libsdl.org/CategoryFilesystem
[vcpkg]: https://github.com/Microsoft/vcpkg
[Homebrew]: https://brew.sh/
[Macports]: https://www.macports.org/
[SDL mercurial repository]: https://libsdl.org/hg.php


[SDL2]: https://www.libsdl.org/download-2.0.php
[SDL_image]: https://www.libsdl.org/projects/SDL_image/
[SDL_mixer]: https://www.libsdl.org/projects/SDL_mixer/
[SDL_net]: https://www.libsdl.org/projects/SDL_net/
[SDL_ttf]: https://www.libsdl.org/projects/SDL_ttf/