### Loader Exceptions

When a shared library cannot be loaded, the loader will throw a sublcass of `DerelictException`, which in turn is a subclass of D's `Exception`. More specifically, a `SharedLibLoadException` is thrown when a shared library is not present on the system path, or fails to load into memory for some other reason. A `SymbolLoadException` is thrown when a function is not found in the shared library. In each case, the exception contains a system-specific error message provided by the operating system API.

When given an array of library names, the loader will attempt to load each of them in turn. It will only throw a `SharedLibLoadException` when the last library name fails to load. When any given symbol fails to load, loading will be aborted and a `SymbolLoadException` thrown immediately. To catch and manipulate any of these excptions, import `derelict.util.exception`.

In some cases, it may be desirable that the loader continue and no exception be thrown when a symbol is missing. For example, when a Derelict package has been updated to support the latest version of a C library, but the user wants to continue supporting the older version of the C library. One option is to use `SharedLibVersion` with any Derelict loader that supports it. See [Loading a Shared Library] for more information. Where `SharedLibVersion` is not supported, there are two possibilites. The recommended approach is to continue using the version of the Derelict package that supports the minimally required version of the C library. The other option is to use a library a feature called _selective symbol loading_.

[Loading a Shared Library]: loader

### Selective Symbol Loading

Selective symbol loading is a means of telling a Derelict loader that it is okay to continue loading a library when a specific symbol is missing. Each loader has a `missingSymbolCallback` property which can be set to either a D delegate or function pointer. The signature in both cases is the same. An example usage follows.

```d
// This is needed for the return type enum.
import derelict.util.exception : ShouldThrow;

import derelict.sdl2.sdl;

ShouldThrow myMissingSymCB( string symbolName )
{
    if( symbolName == "SDL2_SomeFuncIDon'tNeed" )
    {
        return ShouldThrow.No;
    }
    else
    {
        return ShouldThrow.Yes;
    }
}

void main()
{
    // Set the callback *before* calling load
    DerelictSDL2.missingSymbolCallback = &myMissingSymCB;
    DerelictSDL2.load();
}
```

The use of the `ShouldThrow` enum makes it easier to remember which value to return when implementing the callback. A simple `true` or `false` can sometimes be confusing: does `true` mean throw or don't throw? Because of the enum, `derelict.util.exception` must be imported, but there is no confusion.

The important point here is that the callback must be set _before_ calling the `load` method. Otherwise, the loader will have no callback to call. Also note that this is a basic example. There is usually more than one function that must be ignored. When using a `MissingSymbolCallback`, it is imperative to check the change log for each new version of the bound C library to understand what has changed between versions so that a complete set of symbol names can be covered. The [`SharedLibVersion` feature](loader#sharedlibversion), where supported, uses selective symbol loading internally.