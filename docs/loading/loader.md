All Derelict packages have one thing in common. Because each package is a [dynamic binding], it must be loaded at runtime. The interface for this is the same across all packages. The DerelictUtil package contains all of the functionality needed to load a dynamic library at run time. Each binding contains a loader class that extends from `SharedLibLoader`, a class found in `derelict.util.loader`. This class exposes two methods for dynamically loading shared libraries.

Some of the Derelict packages support a static binding configuration that eliminates the need to use a `SharedLibLoader`. Instead, the C library is linked either statically or dynamically when compiling the executable. This page is exclusively about using Derelict packages in the dynamic binding configuration.

[dynamic binding]: ../binding

### A normal load

The method that most developers will use is called `load` and takes no arguments. The specific loader on which this method is called will attempt to find and load a shared library, using a predefined set of library names, on the default system shared library path. Loader instances are created automatically in module constructors and, by convention and with few exceptions, are named according to the Derelict package to which they belong. The following example shows how to load the [SDL library] via [DerelictSDL2].

```D
import derelict.sdl2.sdl;

void main()
{
    DerelictSDL2.load();
}
```

[DerelictSDL2]: ../packages/sdl2
[SDL library]: https://www.libsdl.org

### Custom library path

It is important to understand the system library path for each system an application is intended to support. For example, on Windows the system loader will search for the SDL DLL first in the application directory, then in the current working directory, then in a couple of predefined system directories before falling back on those specified in the `PATH` environment variable (see [MSDN] for more details). On other platforms, the application directory and current working directory typically are not searched at all (see [this page] for Linux and [this one] for Mac OS X).

The default library names are selected for each Derelict package based on common formats for each operating system. Sometimes, it is necessary to bypass the default names. For example, a developer may decide to ship special copies of a shared library using a non-standard name, or storing them in a subdirectory under the application directory. In such a case, an overload of the `load` method should be used. One form accepts an array of strings, each a library name. Another overload takes a single string containing a comma-separated list of library names. Each name can include a path. If no path is specified, the default search path will be used.

```D
import derelict.sdl2.sdl;

version(Windows) libName = "dlls\\MySDL2.dll";
else libName = "MySDL2.so";

void main()
{
    DerelictSDL2.load(libName);
}
```

[MSDN]: http://msdn.microsoft.com/en-us/library/7d83bc18.aspx
[this page]: http://tldp.org/HOWTO/Program-Library-HOWTO/dl-libraries.html
[this one]: https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man3/dlopen.3.html

### SharedLibVersion

Some Derelict loaders, but not all, support version-specific loading. In other words, it's possible to tell the loader to attempt to load a lower version of a shared library than the highest version it supports. An example is DerelictFI.

DerelictFI is a binding to the [FreeImage library]. FreeImage is supported on every major platform. Most Linux distributions make it available through the system package manager, but the version available is usually behind the highest version supported by DerelictFI. A program that simply calls `DerelictFI.load()` on such systems will fail to load the library.

To work around this, a program using DerelictFI can select the minimum required version of the FreeImage library. The lowest supported by DerelictFI is `3.15.0`. If none of the functions added in later versions of FreeImage are needed, then the call to `DerelictFI.load` can be made like so:

```D
DerelictFI.load(SharedLibVersion(3, 15, 0));
```

Check the documentation for any Derelict package you use to determine if it supports loading with `SharedLibVersion` and, if so, which versions are available as arguments. To future-proof your program, it's a good idea to always use `SharedLibVersion` where supported. Simply calling `load()` will always attempt to load the highest version supported. If a Derelict package is updated and you release a new version of your app, the app can fail if your users haven't yet updated the library on their system. By always specifying the version of the library that you need, your app will always load newer releases of a library (though you shouldn't expect a `3.x` loader to load a `4.x` library).

[FreeImage library]: http://freeimage.sourceforge.net