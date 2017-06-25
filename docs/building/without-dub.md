When using an IDE or any build system other than DUB to manage a project using Derelict, the Derelict packages must be manually compiled and configured. 

The first thing to do is to determine which Derelict packages are required. In the example to follow, a hypothetical project using [DerelictGLFW3] and [DerelictGL3] serves as an example. Let's assume a project directory tree that looks like this:

```txt
- rootDir
-- bin
-- libs
-- import
--- derelict
-- source
-- third-party
--- derelict
```

 All Derelict packages have a dependency on DerelictUtil. All three of these packages will need to be obtained and compiled. DUB is the only build tool supported, but there are two recommended ways to obtain the sources.

!!! note "A DerelictUtil caveat"
    Technically, it's not necessary to download and build DerelictUtil. When compiling the other two packages, DUB will fetch the version it needs and stash it in the global cache. However, you still need to get at the DerelictUtil static library and imports when building your program. If you're okay with pulling them out of the global caches (which is in the `AppData\Roaming` user directory on modern Windows sytems, and `~/.dub` on other systems) or pointing your build system to the proper directories, you can skip the instructions related to fetching and compiling DerelictUtil in the following sections.

!!! note "Static binding configurations"
    Some Derelict packages support a static binding configuration. When you want to use them this way, it may not be necessary to compile and link with them. When configured as a static binding, the package has no link-time dependency on DerelictUtil and only needs to be compiled if it implements any custom functions. DerelictGLFW3 does not, so to use it as a static binding, fetch it as described in the following sections, skip the build step, then when compiling your executable make sure to compile with `-version=DerelictGLFW3_Static`. Some packages, such as DerelictSDL2, must still be compiled. In that case, when building the packages, specify the static binding configuration on the `dub` command line, e.g. `build -cderelict-sdl2-static`.

### Cloning packages with git

All of the official Derelict packages reside in repositories on GitHub under the [DerelictOrg] umbrella organization, making each package available via a simple `git clone` command. For our hypothetical project, that would look like this:

```bash
cd rootdir/third-party/derelict
git clone https://github.com/DerelictOrg/DerelictGLFW3.git glfw3
git clone https://github.com/DerelictOrg/DerelictGL3 gl3
git clone https://github.com/DerelictOrg/DerelictUtil util
```

In order to satisfy the DerelictUtil dependency for the DerelictGLFW3 and DerelictGL3 packages, you'll need make sure the DerelictUtil package is properly configured. First, you need to make sure you know which version of DerelictUtil each package needs and that each package depends on the same version of DerelictUtil -- you can't mix DerelictUtil `2.x` with `3.x` in the same project. The documentation page for each package includes a table that displays that information. For our example, let's assume we want DerelictGL3 version `2.0.x` and DerelictGLFW3 version `4.0.x`. Looking at the [DerelictGL3] and [DerelictGLFW3] docs, we see that both depend on version `3.0.x` of DerelictUtil.

In most cases, you'll want to use the latest version of each package. The master branch of each git repository usually corresponds to the latest release branch and, generally, an effort is made to ensure that patch releases are pushed out as soon as `master` is updated so that the two branches always stay in sync. However, now and again changes are allowed to accumulate in `master` over the course of a few days, so there is always a possibility it might be unstable.

To play it safe, go into the new `util` directory and checkout the `3.0` branch:

```bash
cd util
git checkout 3.0
```

Normally, that will serve you just fine. It gives you the source of the latest `3.0.x` patch release and you can update it any time you want via `git pull` to get any new patch releases when they arrive. However, if you want to freeze at a specific patch release, or use an older one, you can checkout a specific release tag. You can use any patch release that satisifies the dependency version contstraint in each of the Derelict packages you intend to use, e.g. DerelictUtil `3.0.0` does not satisfy the constraint `~>3.0.`, but `3.0.2` does. It's recommended to give the new branch a custom name:

```bash
cd util
git checkout tags/v3.0.0 -b 3.0.0-local
```

Once the DerelictUtil source is configured to the proper branch, DUB needs to be told where to find it. For this, we can use `dub add-local`, which takes a directory name and an optional versoin number as an argument. For this example, all we need is the directory name. Since we changed into the `util` directory in the step above, that means simply `.`.

```bash
dub add-local .
```

Of course, if you're still in the parent directory, it would be:

```bash
dub add-local util
```

With that, we're ready to build the DerelictGL3 and DerelictGLFW3 packages. Again, you can choose to build the master branch of each, checkout the latest branch that correponds to the latest release, or checkout a specific tag. For our example, we'll checkout the latest release branches.

Assuming we're already in the `util` directory:

```bash
dub build -b release -ax86_64
cd ../gl3
git checkout 2.0
dub build -brelease -ax86_64
cd ../glfw3
git checkout 4.0
dub build -brelease -ax86_64
```

This will build optimized 64-bit versions of each library. To build debug versions, omit the `-brelease` flag. To build 32-bit binaries, omit the `-ax86_64`. On Windows, to build 32-bit COFF binaries, replace `ax86_64` with `x86_mscoff`.

You'll now see the files like `util\lib\DerelictUtil.lib` on Windows and `util/lib/libDerelictUtil.a` elsewhere. To use the packages in your project, you need to configure your build tool or IDE to find these libraries along with the source. The import path needs to be set to include the `source` directory of each packge, e.g. `glfw3/source`, and the library path the `lib` directory of each package, e.g. `glfw3/lib`. Alternatively, you can copy all of the source files to a common root directory, do the same for the library files, and add one directory each to the import and library path configurations in your build tool instead of three.

[DerelictOrg]: https://github.com/DerelictOrg

### Fetching packages with dub

Using the same hypothetical project as the example in the previous section, we'll see how to obtain DerelictGL3 `2.0.0`, DerelictGLFW3 `4.0.0`, and DerelictUtil `3.0.0` via `dub`. Note that using this approach is akin to cloning the repositories with git and checkout out a specific git tag.

Starting from the same project root directory as above, the following commands will manually fetch each package.

```
cd derelict
dub fetch --cache=local derelict-glfw3 --version=4.0.0
dub fetch --cache=local derelict-gl3 --version=2.0.0
dub fetch --cache=local derelict-util --version=3.0.0
```

The `--cache=local` argument causes each package to be installed in subdirectories named using the `package-name-x.x.x` format, e.g. `derelict-glfw3-4.0.0`. Each of these directories will cotian a subdirectory that matches the DUB package name, e.g. `derelict-glfw3-4.0.0/derelict-glfw3`. This second subdirectory is the package root. 

As before, DUB needs to be told where to find DerelictUtil.

```
dub add-local derelict-util-3.0.0/derelict-util
```

With that, all three packages are ready to build.

```
cd derelict-glfw3-4.0.0/derelict-glfw3
dub build -brelease -ax86_64
cd ../derelict-gl3-2.0.0/derelict-gl3
dub build -brelease -ax86_64
cd ../derelict-util-3.0.0/derelict-util
dub build -brelease -ax86_64
```

This will build optimized 64-bit versions of each library. To build debug versions, omit the `-brelease` flag. To build 32-bit binaries, omit the `-ax86_64`. On Windows, to build 32-bit COFF binaries, replace `ax86_64` with `x86_mscoff`.

You'll now see the files like `derelict-util-3.0.0\derelict-util\lib\DerelictUtil.lib` on Windows and `derelict-util-3.0.0/derelict-util/lib/libDerelictUtil.a` elsewhere. To use the packages in your project, you need to configure your build tool or IDE to find these libraries along with the source. The import path needs to be set to include the `source` directory of each packge, e.g. `derelict-glfw3-4.0.0/derelict-glfw3/source`, and the library path the `lib` directory of each package, e.g. `derelict-glfw3-4.0.0/derelict-glfw3/lib`. Alternatively, you can copy all of the source files to a common root directory, do the same for the library files, and add one directory each to the import and library path configurations in your build tool instead of three.

!!! note "For Linux"
    When compiling executables that use Derelict dynamic bindings on Linux, it's necessary to link with `libdl`. This is used by DerelictUtil to load shared libraries dynamically at runtime.

Failure to properly configure the build system will result in compilation or linker errors. It is beyond the scope of this documentation to describe how to configure the various IDEs and build tools people may use with D. Learning to do so is entirely up to the user.

[code.dlang.org]: https://code.dlang.org
[DerelictGLFW3]: ../packages/glfw3
[DerelictGL3]: ../packages/gl3

