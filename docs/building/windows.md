On all of the systems D supports but one, developers can rely on a standard ecosystem of compiler toolchains, including linkers. The three major D compilers all use the system linker on those systems. The outlier is Windows. 

When using any Derelict package as a dynamic binding, none of the information on this page applies. It only applies when using any static binding, whether it be one of the Derelict packages that supports such a configuration or a non-Derelict library. In other words, when developing D programs that have a link-time dependency with third-party libraries on Windows, a few prerequisites need to be understood.

Of the three main compilers, only DMD and LDC actively support Windows. GDC has not had a Windows release for some time (though that will likely change now that the compiler has been accepted into the GCC compiler suite). The following sections look at each compiler in turn.

Download links for all three compilers can be found at the [dlang.org downloads page].

[dlang.org downloads page]: https://dlang.org/download.html

### DMD

DMD is the reference D compiler, developed primarily by D's creator and [Digital Mars] owner, [Walter Bright].

DMD is built on top of the same compiler backend as the Digital Mars C/C++ compiler, [DMC]. It ships with [OPTLINK], an ancient linker that only knows how to link 32-bit executables composed of object files in the [OMF] format. When passing the `-m32` command line switch (the default for the 32-bit version of DMD, currently the only version distributed in binary form on Windows), DMD's object output will be in the OMF format and it will use the OPTLINK linker to create executables.

Thankfully, DMD also supports generating object files in the [COFF] format and linking using the Microsoft linker. This is enabled via the `-m32mscoff` command line switch for 32-bit binaries, and `-m64` for 64-bit binaries.This requires the installation of either the Microsoft Build Tools and the Windows SDK, or a supported version of Visual Studio. If you do not already have any of these installed, the easiest thing to do is to install the latest Visual Studio [Visual Studio Community Edition].

!!! note
    Generally, you can expect Visual Studio versions 2012 and later to be supported, though sometimes new versions may temporarily break support in some way. For example, due to changes in the path structure in VS 2017, the DMD installer could not automatically identify it for a time, so it could not properly configure DMD. Paths can always be configured manually, but VS 2015 renamed the C runtime library, a breakage that can only be fixed by the DMD developers. Any breakage is generally fixed within a DMD release or two. In the interim, the previous Visual Studio version should continue to work.

To reiterate, when using the dynamic binding configuration of a Derelict package, the DLL can be in any format and compiled with any Windows compiler. As long as 32-bit executables are loading 32-bit DLLs, and 64-bit executables are loading 64-bit DLLs, the object file format is irrelevant. It only applies when linking in a static binding configuration.

[Digital Mars]: https://digitalmars.com/
[Walter Bright]: http://walterbright.com/
[DMC]: https://digitalmars.com/download/freecompiler.html
[OPTLINK]: http://www.digitalmars.com/ctg/optlink.html
[OMF]: https://en.wikipedia.org/wiki/Relocatable_Object_Module_Format
[COFF]: https://en.wikipedia.org/wiki/COFF
[Visual Studio Community Edition]: https://www.visualstudio.com/downloads/

#### COFF

Once the Microsoft linker and libraries are installed, DMD needs to be told where to find them. While it's possible to edit the DMD configuration file (`sc.ini` in the `dmd2\windows\bin` directory), it's much easier to let the DMD Windows installer handle it automatically. When everything is configured, any libraries that have a link-time dependency must be in the COFF format.

Effectively, this means that when using a Derelict package in a static binding configuration (where supported), you should always either choose binary packages compiled with Visual C++, or use Visual C++ to compile the library yourself. For example, to use the [DerelictSDL2] static binding configuration, go to [libsdl.org] and download the appropriate SDL development libraries for Visual C++, e.g. `SDL-devel-2.0.5-VC.zip`.

[DerelictSDL2]: ../packages/sdl2
[libsdl.org]: https://www.libsdl.org/download-2.0.php

#### OMF

Why are you even thinking about compiling in OMF format? Just don't! But if you must, don't even bother to look for precompiled OMF binaries for any library to which Derelict binds. They may be out there somewhere, but you're better off either running an object converter or compiling yourself with DMC. The former is the recommended approach, as few libraries have a build system that supports DMC out of the box.

The first thing to do is to head to the Digital Mars site to download the Extended Utility Package ([EUP]). This contains two tools that will do the job. When you are linking dynamically with a DLL import library, run the `coffimplib` utility on the COFF DLL (such as the `SDL2.dll` obtained in the VC development archive from the SDL downloads page). This will produce an import library in OMF format that will link with the COFF DLL. When linking statically, run `coff2omf` on the COFF static library or any COFF object files you need to link with. This will produce libraries or object files in the OMF format.

[EUP]: https://digitalmars.com/eup.html

### LDC

Older LDC releases for Windows were built on top of [mingw-w64], but recent releases require the Microsoft tools and libraries. The same COFF instructions in the DMD section above apply here.

[mingw-w64]: https://mingw-w64.org/doku.php

### GDC

There has not been a GDC release for Windows in some time. If there eventually is, it will assumedly be built on top of mingw-w64. In that case, only binaries compiled with mingw-w64, either downloaded in precompiled packages or compiled yourself, will be compatible. While the MinGW linker does support COFF, and can sometimes link with binaries compiled with Visual C++, there are often linker errors that need to be worked around. Best not to mix them. 

