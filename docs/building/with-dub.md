When using DUB to manage a project, it is not neccesary to clone any of the Derelict packages in git. DUB will take care of pulling down the source of the Derelict dependencies, compiling them, and linking them with the executable. It can also make sure that the very latest release of the are used. A great many DUB-enabled libraries other than Derelict can be found in the [DUB Package Registry], all of which can also be automatically compiled and linked with a project simply by editing a configuration file.

When creating a project for DUB to manage, a configuration file of the name **dub.json** or **dub.sdl** must be provided. This documentation is not a tutorial on general DUB use, so to learn more about how to configure compile settings and more, please see the [DUB package format documentation]. To find help with using DUB, vist the [DUB forums].

To use any package from the DerelictOrg collection in your project, you'll simply need to add the package to your project configuration as a dependency. When you execute `dub` or `dub build` in the directory where your project configuration lives, DUB will make sure that the dependencies are installed, compiled and linked. You can upgrade to the latest version of each of your project's dependencies by executing ``dub upgrade`` in the same directory.

All packages from DerelictOrg are registered in the DUB registry under names that are formatted in a specific way, derived from the package name. For example, the package DerelictSDL2 is registered as `"derelict-sdl2"` and the package DerelictGL3 as `"derelict-gl3"`. Every official Derelict package follows that pattern.

Some people maintain Derelict-style packages, using DerelictUtil as a foundation, outside of the DerelictOrg group. These projects often use a similar format for registered names, with the addition of the word `"extras"`. For example, a third-party package called DerelictFoo might be registered as `"derelict-extras-foo"`. While this approach is encouraged, not all third-party packages use this format. Some use the same `"derelict-foo"` format as the "official" packages, others might use something else entirely. Please be aware that the DerelictOrg maintainers are not responsible for such packages and any support requests should be directed to the person or persons who are.

To enable any package from the DUB registry, including the Derelict packages, in a DUB-managed project, simply add the registered package name, coupled with a version string, as a dependency in the project configuration. For example, a project using both DerelictSDL2 and DerelictGL3 would have something like the following in its `dub.json`:

```
"dependencies": {
    "derelict-sdl2": "~>2.0.0",
    "derelict-gl3": "~>1.0.18"
}
```

Or, when using `dub.sdl` instead:

```
dependency "derelict-sdl2" version="~>2.0.0"
dependency "derelict-gl3" version="~>1.0.18"
```

This specifies a version of DerelictSDL2 _greater than or equal to_ `2.0.0` and _less than_ `2.1.0`, and similarly for DerelictGL3 `1.0.18` - `1.1.0`. When compiling a project for the first time, DUB will check the local system for any version matching the constraints and, if none is found, will fetch the latest from git that does match. Later, when running `dub upgrade`, DUB will look to see if a newer version which matches the constraints is available. For more information on DUB dependencies, see the [DUB package format documentation] and the [DUB Wiki page] on version management.

[DUB Package Registry]: https://code.dlang.org/
[DUB package format documentation]: https://code.dlang.org/package-format
[DUB forums]: forum.rejectedsoftware.com/groups/rejectedsoftware.dub/
[DUB Wiki page]: https://github.com/dlang/dub/wiki/Version-management
