When using DUB to manage a project, it is not neccesary to clone any of the Derelict packages with git. DUB will take care of pulling down the source of the Derelict dependencies, compiling them, and linking them with the executable. A number of DUB-enabled libraries other than Derelict can be found in the [DUB Package Registry], all of which can also be automatically compiled and linked with a project simply by editing a configuration file.

### General configuration

!!! note
    None of the C libraries to which the Derelict libraries bind are bundled with the packages. The binaries for the C libraries must be obtained separately, either through downloading precompiled binaries from project websites, repositories, or third-party packagers, or by downloading and obtaining the source. Where possible, suggestions on how to obtain binaries and/or source are included in the package-specific documentation.

When creating a project for DUB to manage, a configuration file of the name **dub.json** or **dub.sdl** must be provided. This documentation is not a tutorial on general DUB use, so to learn more about using DUB from the command line and configuring DUB packages, please see the [DUB documentation]. For more DUB assitance, visit the [DUB forums].

To use any package from the DerelictOrg collection in your project, you'll simply need to add it to your project configuration as a dependency. When you execute `dub` or `dub build` in your project configuration's root directory, DUB will make sure that the dependencies are installed, compiled and linked. You can upgrade to the latest version of each of your project's dependencies by executing `dub upgrade` in the same directory.

All packages from DerelictOrg are registered in the DUB registry under names that are formatted in a specific format derived from the package name. For example, the package DerelictSDL2 is registered as `derelict-sdl2` and the package DerelictGL3 as `derelict-gl3`. Every Derelict package under the DerelictOrg umbrella follows that pattern.

Some people maintain third-party Derelict-style packages, using DerelictUtil as a foundation, outside of the DerelictOrg group. Some of these packages are registred with names using a format that puts `derelict-extras` in front of the package name. For example, a third-party package called DerelictFoo might be registered as `derelict-extras-foo`. While this approach is encouraged, many such packages are still registered as `derelict-foo`. Still others might use another format entirely. Please be aware that the DerelictOrg maintainers are not responsible for such packages and any support requests should be directed to the person or persons who are via the links to their source repositories from the package pages at the [DUB Package Registry].

To enable any package from the DUB registry, including the Derelict packages, in a DUB-managed project, simply add the registered package name, coupled with a version string, as a dependency in the project configuration. For example, a project using both DerelictSDL2 and DerelictGL3 would have something like the following in its configuration:

**dub.json**
```json
"dependencies": {
    "derelict-sdl2": "~>3.0.0",
    "derelict-gl3": "~>2.0.0"
}
```

**dub.sdl**
```bash
dependency "derelict-sdl2" version="~>3.0.0"
dependency "derelict-gl3" version="~>2.0.0"
```

This specifies a version of DerelictSDL2 _greater than or equal to_ `2.0.0` and _less than_ `2.1.0`, and similarly for DerelictGL3 `1.0.18` - `1.1.0`. When compiling a project for the first time, DUB will check the local system for any version matching the constraints and, if none is found, will fetch the latest from git that does match. Later, when running `dub upgrade`, DUB will look to see if a newer version which matches the constraints is available. For more information on DUB dependencies, see the [DUB documentation] and the [DUB Wiki page] on version management. See the page [Latest Versions] for a table of all of the latest release version tags for the supported DerelictOrg packages.

[DUB Package Registry]: https://code.dlang.org/
[DUB documentation]: https://code.dlang.org/getting_started
[DUB forums]: forum.rejectedsoftware.com/groups/rejectedsoftware.dub/
[DUB Wiki page]: https://github.com/dlang/dub/wiki/Version-management
[Latest Versions]: ../packages/latest

### Dynamic bindings

All Derelict packages are configured as dynamic bindings by default (see [this page] to understand the difference between static and dynamic bindings). This does not mean they require dynamic linking. There is no link-time dependency at all. Instead, the C shared library is loaded manually at runtime with a call to a `load` function on a loader instance, e.g. `DerelictSDL2.load`.

For this to work, the shared library needs to be on the system path. On Posix systems, that usually means installing from the package manager. It is not necessary to install the development packages for this to work, given the lack of a link-time dependency. For example, Ubuntu users can install SDL via `sudo apt-get install libsdl2-2.0`, but need not install `libsdl2-dev`. On Windows, this usually means placing the C DLL in the executable's directory, though [Derelict loaders] support loading from custom locations, e.g. `DerelictSDL2.load("dlls/SDL2.dll")`.

[this page]: ../bindings
[Derelict loaders]: ../loading/loader

### Static bindings

Derelict packages that support a static binding configuration can be configured using a `subConfiguration` entry in the project config, or a `versions` entry. The subconfigurations are in the format of `package-name-static`, e.g. `derelict-sdl2-static`. This is the preferred approach, as it completely eliminates the dynamic binding modules from the build.

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

Versions can be `Derelict_Static`, which enables the static binding configuration for all packages in a project that support it, or `PackageName_Static`, e.g. `DerelictSDL2_Static` which enables it for the specific package.

**dub.json**
```json
"versions": ["DerelictSDL2_Static"]
```

**dub.sdl**
```bash
versions "DerelictSDL2_Static"
```

Static binding configurations require linking either dynamically or statically with the library at runtime. Generally, on Posix systems this requires installing the development version of the C library through a package manager or compiling from source. The appropriate libraries will be installed on the system path. On Windows, it requires downloading the precompiled development packages or compiling from source, making sure the linker can find the library, and, when linking dynamically, ensuring the DLL is in the compiled executable's directory.

The documentation for each package that supports static binding provides package-specific details.

