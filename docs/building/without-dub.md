When using a build system besides DUB, or an IDE, to manage a Derelict project, the first thing to do is to determine which Derelict packages are required. In the example to follow, a project using DerelictGLFW3 and DerelictGL3 is assumed. Both, like all Derelict packages, have a dependency on DerelictUtil. All three of these packages will need to be obtained manually. One way is to clone them from their git repositories, but then the correct branches must be selected for the specific versions of each package required by the project. It's much simpler to perform a manual fetch with DUB and let it grab the correct branches instead.

It's probably best to use the latest version of DerelictGLFW3 and DerelictGL3. The latest version can be determined by visiting [code.dlang.org] and searching for `derelict-glfw3` and `derelict-gl3` on that page. Currently, that is `3.1.0` for the former and `1.0.19` for the latter. It's also necessary to determine which version of DerelictUtil to build against. It should be a version compatible with all of the Derelict packages that will be used with the project. For this example, `2.0.4` is used.

The following commands will manually fetch each package.

```
mkdir derelict
cd derelict
dub fetch --cache=local derelict-glfw3 --version=3.1.0
dub fetch --cache=local derelict-gl3 --version=1.0.19
dub fetch --cache=local derelict-util --version=2.0.4
```

This will install each package in subdirectories named using the `package-name-x.x.x` format, e.g. `derelict-glfw3-3.1.0`. Both DerelictGLFW3 and DerelictGL3 will need to know where to find the source for DerelictUtil. When manually fetching packages like this, they are not automatically added to the search path, so DUB must be told with the following command.

```
dub add-local derelict-util-2.0.4
```

With that, all three packages are ready to build.

```
cd derelict-glfw3-3.1.0
dub build -brelease
cd ../derelict-gl3-1.0.19
dub build -brelease
cd ../derelict-util-2.0.4
dub build -brelease
```

This will build optimized versions of each library. To build debug versions, omit the `-brelease` flag. To explicitly build 64-bit binaries, add `-ax86_64` to each command line.

From here on out, assume **$DERELICT** is the path to the derelict directory, whether that is **C:\derelict** or **C:\dev\derelict** or whatever on Windows, or **~/dev/derelict** or whatever on other platforms. Most IDEs and build tools on Windows can work with forward slashes, but change them to backslashes in the following paths if needed.

At this point, a platform-specific library should exist in the **lib** sudirectory of each package. In order to compile a project using these libraries, the compiler will first need to be able to find the Derelict source modules so that it can parse the import statements it encounters. The following paths are the ones which must be configure in whatever build system is being used.

```
$DERELICT/derelict-glfw3-2.0.0/source
$DERELICT/derelict-gl3-1.0.18/source
$DERELICT/derelict-util-2.0.4/source

```

In order to link an executable, the linker will need to know which libraries to link with and where to find them. The libraries needed in this example are shown below, along with their full paths. These will need to be configured appropriately in the build system.

On Windows the libraries are in the **DerelictFoo.lib** format.

```
$DERELICT\derelict-glfw3-2.0.0\lib\DerelictGLFW3.lib
$DERELICT\derelict-gl3-1.0.18\lib\DerelictGL3.lib
$DERELICT\derelict-util-2.0.4\lib\DerelictUtil.lib
```

On POSIX systems the libraries are in the **libDerelictFoo.a** format.

```
$DERELICT/DerelictSDL2/lib/libDerelictSDL2.a
$DERELICT/DerelictGL3/lib/libDerelictGL3.a
$DERELICT/DerelictUtil/lib/libDerelictUtil.a
```

It may prove convenient to create a common **$DERELICT/lib** directory, copy all of the libraries there, and then configure the build system to search for all libraries in that path. Then the path need not be included when specifying each library in the build system's configuration.

**Note For Non-Windows Systems:** When building on Mac, Linux, or one of the BSDs), it may also be necessary to link with **libdl**. This is used by DerelictUtil to load shared libraries dynamically at runtime.

Failure to properly configure the build system will result in compilation or linker errors. It is beyond the scope of this documentation to describe how to configure the various IDEs and build tools people may use with D. Learning to do so is entirely up to the user.

[code.dlang.org]: https://code.dlang.org

