DerelictGL3 is a dynamic binding to the [OpenGL] library.

Due to the nature of OpenGL, this package differs from other Derelict packages in a few ways. These differences are described in the relevant sections below.

[OpenGL]: https://www.opengl.org

### Releases

The DUB package name of DerelictGL3 is `derelict-gl3`.

The current DUB package version is `~>2.0.0-beta`.

Unlike other Derelict packages, DerelictGL3 releases do not correspond to any specific version of the C library. All versions of OpenGL are always supported, even though the `SharedLibVersion` construct is not. When a new version of OpenGL is released, support is added to the current DerelictGL3 release series and the patch number, rather than the minor number, is bumped in the new release version.

| DerelictGL3 Version   | git Branch     | OpenGL Version | DerelictUtil Version | Supported |
| --------------------- | ----------     | ------------   | -------------------- | --------- |
| 2.0.0-beta.1          | [master]/[2.0] | 3.2.x          | 3.0.x                | &#x2714;  |
| 1.0.x                 | [1.0]          | 1.0 - 4.5      | 1.0.x - 3.0.x        | &#x2714;  |

The `1.0.x` series is compatible with all DerelictUtil versions, as long as it is compiled against and linked with the same version of DerelictUtil as an executable. Unfortunately, in a oversight lost to time, the `1.0.x` series uses a `derelict-util` dependency of `>=1.0.3` which disallows anything less than version `1.0.3`, but has no contraint on later versions. As such, it's possible to independently compile the DerelictGL3 library against version `1.0.x` or `2.0.x` of DerelictUtil, then attempt to link with DerelictUtil `3.0.x` when building the executable.

It's recommended to use DerelictGL3 `1.0.x` only when you need compatibility with the `2.0.x` series of DerelictUtil because of other Derelict packages. If you compile DerelictGL3 `1.0.x` independently, make sure you specify version `2.0.x` of DerelictUtil in your `dub.selections.json` file. Otherwise, DUB will pull down the latest DerelictUtil and link against it.

DerelictGL3 `3.0.x` was overhauled to provide compile-time configuration of supported OpenGL versions and extensions. It also provides the option of wrapping all function declarations in a `struct`, either automatically or in a custom type, to provide more efficient context management. All new projects should use this version. Support for the `1.0.x` series will be discontinued at some point in the future.

[master]: https://github.com/DerelictOrg/DerelictGL3/tree/master
[2.0]: https://github.com/DerelictOrg/DerelictGL3/tree/2.0
[1.0]: https://github.com/DerelictOrg/DerelictGL3/tree/1.0

### Using DerelictGL3

See the generic documentation on [Compiling and Linking] for generic information on incorporating any Derelict package in your project.

[Compiling and Linking]: ../building/overview

#### Obtaining the binaries

OpenGL is available on Windows and Mac OS X out of the box. Windows users should upgrade their graphics card drivers to always have the latest version. Linux and *BSD users should consult their OS knowlege base or support forums to determine enable hardware-accelerated OpenGL.

#### Configuring OpenGL versions

Out of the box, DerelictGL3 will attempt to load all available OpenGL versions as free functions, i.e. not wrapped in a struct. This can be overridden by adding a `versions` directive to a project's DUB configuration with one of the following values.

-----------------------------

`DerelictGL3_CustomFreeFuncs`

-----------------------------

When this version is specified, the DerelictGL3 modules will not declare any types or function pointers. This must be handled by the user using a mixin template, `glFreeFuncs`. The following enables OpenGL versions 3.3 and lower:

```d
module mygl;
public import derelict.opengl;
mixin glFreeFuncs!(GLVersion.gl33);
```
This will enable all of the OpenGL types and function pointers for every version up to OpenGL 3.3 into the `mygl` module. Modules that need to call OpenGL functions should import `mygl`.

`glFreeFuncs` takes an optional parameter that will include deprecated types and function pointer declarations. This is `false` by default, but can be enabled with, e.g., `mixin glFreeFuncs(GLVersion.gl33, true);`.

With no arguments, `glFreeFuncs` will enable all supported versions of OpenGL without deprecations. This is the default behavior.

----------------------------

`DerelictGL3_Contexts`

----------------------------

This will configure DerelictGL3 to load the OpenGL function pointers into a `struct`. To use the default implementation, mixin the `glContext` template along with the desired version:

```d
module mygl;
public import derelict.opengl;
mixin glContext!(GLVersion.gl33);
GLContext context;
```

This will enable all of the types and function pointers for every version up to OpenGL 3.3 into the `mygl` module, wrapping the function pointers (but not the types) into a structure named `GLContext`, and create an instance of that type named `context`. OpenGL functions can then be called via the `context` instance, e.g. `context.glClear(GL_COLOR_BUFFER_BIT);`.

Deprecated types and functions can be enabled by passing `true` as a second argument to `glContext`. With no arguments, `glContext` will load all supported versions of OpenGL without deprecations (the default behavior).

Custom contexts are also supported, but they require a bit of string and template `mixin` boilerplate.

```d
module mygl;
public import derelict.opengl;

enum maxGLVersion = GLVersion.gl33;
enum supportDeprecated = false;

// Required imports
static if(!supportDeprecated) mixin(glImports);
else mixin(gl_depImports);

// Type declarations should be outside of the struct
mixin glDecls!(maxGLVersion, supportDeprecated);
struct MyContext {
    mixin glFuncs!(maxGLVersion, supportDeprecated);
    mixin glLoaders!(maxGLVersion, supportDeprecated);
}
MyContext context;
```

This is essentially what is done by the default `glContext` mixin. Note that in `glDecls`, `glFuncs`, and `glLoaders`, the second parameter is optional, with the default value of `false`.

### Loading the OpenGL library

The steps required to gain access to different OpenGL versions may differ across operating systems. For example, on Windows OpenGL versions up to 1.1 may be dynamically linked, but 1.2 and above must be loaded dynamically at runtime after a context has been created. On Mac OS X, all of the OpenGL versions may be dynamically linked or dynamically loaded, and in the latter case a context need not be active.

To hide these differences and present a unified interface on all supported platform, DerelictGL3 requires a three-step process. When using the free function configuration, those steps are:

1. **Call `DerelictGL3.load`.** 
This must be done before any OpenGL functions are called, but may happen before or after a context has been created, but must be done before calling `DerelictGL3.reload` in the last step. This will load the OpenGL shared library and all OpenGL functions up to version 1.1. Failure to load will result in an exception.
2. **Create an OpenGL context.** 
This may occur before or after `DerelictGL3.load` is called, but must be done before calling `DerelictGL3.reload` in the next step. DerelictGL3 does not provide the means of creating an OpenGL context. For that, use either the system API (like Win32) or a third-party library such as [SDL], [GLFW], or [SFML].
3. **Call `DerelictGL3.reload`.**
This must occur after `DerelictGL3.load` as been called and after an OpenGL context has been created. This will load all versions that have been configured from 1.2 and up, as well as any configured extensions. For maximum portability, this function should be called every time the OpenGL context is changed.

The following example demonstrates. It uses the default DerelictGL3 free function configuration. It does not show the code to create the context.

```d
import derelict.opengl;
void main() {
    // Load the OpenGL shared library
    DerelictGL3.load();

    // Create the context using a system or third-party API
    myCreateContext();

    // Now load all supported functions
    DerelictGL3.reload();
}
```

The steps are similar when using the context object configuration.

1. **Call `DerelictGL3.load`.** 
This must be done before any OpenGL functions are called, but may happen before or after a context has been created, but must be done before calling `load` on a context object in the last step. This will load the OpenGL shared library but does not load any OpenGL functions. Failure to load will result in an exception.
2. **Create an OpenGL context.** 
This may occur before or after `DerelictGL3.load` is called, but must be done before calling `load` on a context object in the next step. DerelictGL3 does not provide the means of creating an OpenGL context. For that, use either the system API (like Win32) or a third-party library such as [SDL], [GLFW], or [SFML].
3. **Call `load` on a context object instance.**
This must occur after `DerelictGL3.load` as been called and after an OpenGL context has been created. For maximum portability, context objects should be created for and associated with each OpenGL context used in the program. Then, the `load` method can be called on each instance just once. This will load all configured OpenGL functions from 1.0 and up and any extensions that have been configured. When the OpenGL context changes, functions can be called through its associated object instance.

The following examples demonstrates, using the default `GLContext` type.

```d
import derelict.opengl;
mixin glContext!(GLVersion.gl33);
GLContext myContext;

void main() {
    // Load the OpenGL shared library
    DerelictGL3.load();

    // Create the context using a system or third-party API
    myCreateContext();

    // Now load all supported functions
    myContext.load();
}
```

Both `DerelictGL3.reload` and the `load` method of a context instance return the highest version of OpenGL that was sucessfully loaded in the form of a `GLVersion` value. You can also obtain the same value by calling calling `loadedVersion` on the `DerelictGL3` instance or on the context object.

The following loads the highest supported version, but requires OpenGL 3.3 at a minimum.

```d
import derelict.opengl;
void main() {
    DerelictGL3.load();
    myCreateContext();
    auto loaded = DerelictGL3.reload();
    if(loaded < GLVersion.gl33) {
        throw new Exception("OpenGL version too low!");
    }
}
```

#### Extensions

Whether using the default configuration or a custom one, no extensions are loaded by default. Extensions must be individually declared using string mixins unless they are part of the core specification of an OpenGL version you have configured.

!!! note
    To see which extensions are loaded along with the core versions, see the `core_*.d` files in the DerelictGL3 source directory [source/derelict/opengl/extensions]. Eventually, they will all be listed in this document.

To demonstrate, let's assume you want to enable the highest OpenGL version available and you want to make use of the OpenGL 4.3 function `glMultiDrawArraysIndirect`. This function is part of the `GL_ARB_multi_draw_indirect` extension.

```d
import derelict.opengl;

bool hasMultiDrawIndirect;

void main() {
    DerelictGL3.load();
    myCreateContext();
    auto loaded = DerelictGL3.reload();
    hasMultiDrawIndirect = (loaded >= GLVersion.gl43) 
        || DerelictGL3.isExtensionLoaded("GL_ARB_multi_draw_indirect");
} 
```

`isExtensionLoaded` is also available on context objects, e.g. `myContext.isExtensionLoaded(extName);`.

When you are using an extension that has not been promoted to core, or you have configured DerelictGL3 to use a version lower than the one in which the extension was promoted, you must declare the extension. For the free function API, this is a one liner. For example, if you've configured OpenGL 4.1 as the highest version, but want to support the `GL_ARB_multidraw_indirect` extension:

```d
import derelict.opengl;
mixin glFreeFuncs!(GLVersion.gl41);

// GL_ARB_multi_draw_indrect type and function pointer declarations
mixin(arbMultiDrawIndirect);

bool hasMultiDrawIndirect;

void main() {
    DerelictGL3.load();
    myCreateContext();
    DerelictGL3.reload();
    hasMultiDrawIndirect = 
            DerelictGL3.isExtensionLoaded("GL_ARB_multi_draw_indirect");
}
```

To use extensions with context objects, you must define a custom context type. It is not currently possible to mix extension in to the default `GLContext`. Each extension declaration must be split into three parts: types, functions, and the loader.

```d
module mygl;
public import derelict.opengl;

enum maxGLVersion = GLVersion.gl41;
enum supportDeprecated = false;

// Required imports
static if(!supportDeprecated) mixin(glImports);
else mixin(gl_depImports);

// Core type declarations
mixin glDecls!(maxGLVersion, supportDeprecated);

// Extension type declarations
mixin(arbMultiDrawIndirectDecls);

struct MyContext {
    // Core functions
    mixin glFuncs!(maxGLVersion, supportDeprecated);

    // Extension functions
    mixin(arbMultiDrawIndirectFuncs);
    
    // Core loaders
    mixin glLoaders!(maxGLVersion, supportDeprecated);

    // Extension loaders
    mixin(arbMultiDrawIndirectLoaders);
}

MyContext context;
```

With this, after `DerelictGL3.load` has been called and a context created, then calling `context.load()` will load any mixed in extensions that are available. Then `context.isExtensionLoaded` can be called to test for availability.

[source/derelict/opengl/extensions]: https://github.com/DerelictOrg/DerelictGL3/tree/master/source/derelict/opengl/extensions


[SDL]: sdl2
[GLFW]: glfw3
[SFML]: sfml2




