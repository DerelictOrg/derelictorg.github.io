DerelictSFML2 provides both [static and dynamic bindings] the 2.x series of [SFML (Simple and Fast Multimedia Library)]. DerelictSFML2 does not support Derelict's [SharedLibVersion] feature.

SFML is a C++ library. Although D has some support for binding to C++, Derelict packages only bind to C APIs. The SFML project provides such an API in the form of [CSFML]. When using DerelictSFML2, the CSFML distribution is required.

SFML consists of five modules, all of which are bundled as independent libraries in the same distribution and share the same version number: System, Window, Graphics, Audio and Network. Each library has a corresponding Derelict loader.

[static and dynamic bindings]: ../bindings
[SharedLibVersion]: ../loading/loader/#ShaderLibVersion
[SFML (Simple and Fast Multimedia Library)]: https://www.sfml-dev.org/
[CSFML]: https://github.com/SFML/CSFML

### Releases

The DUB package name of DerelictSFML2 is `derelict-sfml2`.

The current DUB package version is `~>4.0.0-beta`.

The following table shows the correlation between DerelictSFML2 releases (the most recent patch release of each `major.minor` series), its corresponding git branch, SFML versions, and DerelictUtil versions. The latest release is listed at the top and is the recommended version. When using DerelictSFML2 with other Derelict packages, please ensure all of the Derelict packages use the same DerelictUtil `major.minor` series.

| DerelictSFML2 Version  | git Branch     | CSFML  Version | DerelictUtil Version | Supported |
| ---------------------  | ----------     | -------------- | -------------------- | --------- |
| 4.0.0-beta             | [master]/[4.0] | 2.4            | 3.0.x                | &#x2714;  |
| 3.0.2                  | [3.0]          | 2.3            | 2.0.x                | &#x2714; (bugfix only)  |
| 2.0.5                  | [2.0.0]        | 2.2            | 2.0.x                | &#x2716;  |
| 1.2.1                  | [2.2]          | 2.2            | 1.0.x                | &#x2716;  |
| 1.1.0                  | [2.1]          | 2.1            | 1.0.x                | &#x2716;  |
| 1.0.0                  | [2.0]          | 2.0            | 1.0.x                | &#x2716;  |

[master]: https://github.com/DerelictOrg/DerelictSFML2/tree/master
[4.0]: https://github.com/DerelictOrg/DerelictSFML2/tree/4.0
[3.0]: https://github.com/DerelictOrg/DerelictSFML2/tree/3.0
[2.0.0]: https://github.com/DerelictOrg/DerelictSFML2/tree/2.0.0
[2.2]: https://github.com/DerelictOrg/DerelictSFML2/tree/2.2
[2.1]: https://github.com/DerelictOrg/DerelictSFML2/tree/2.1
[2.0]: https://github.com/DerelictOrg/DerelictSFML2/tree/2.0

### Using DerelictSFML2

See the generic documentation on [Compiling and Linking] for generic information on incorporating any Derelict package in your project.

[Compiling and Linking]: ../building/overview

#### Obtaining the CSFML binaries

OS X and Windows binaries of all releases of CSFML, from 2.0 to the latest, are available from the [CSFML download page].

Linux binaries for some releases are available from the same page, but newer releases must either be built from source or installed from the system package manager where available.

!!! note
    As DerelictSFML2 does not support a static binding configuration, there's no need to install the development version of the SFML packages from any package manager.

The CSFML source is avaliable from the same download page and can also be obtained from [GitHub]. The steps required to compile CSFML on different platforms should be similar to those described in the instructions for compiling SFML at the [SFML wiki].

[CSFML download page]: https://www.sfml-dev.org/download/csfml/
[GitHub]: https://github.com/SFML/CSFML
[SFML wiki]: https://github.com/SFML/SFML/wiki/Tutorials

#### Loading the CSFML libraries

DerelictSFML2 is a dynamic binding, so the functions from the CSFML libraries must be loaded at runtime via a call to the appropriate loader. 

Note that unlike most other Derelict packages, there is no loader in DerelictSFML2 that matches the package name, i.e. you will never call `DerelictSFML2.load`. Instead, each SFML library has a corresponding loader named with the format `DerelictSFML2Library`, e.g. `DerelictSFML2System`.

Each library has its own module, so they can be imported individually like so:

```d
import derelict.sfml2.system,
       derelict.sfml2.window;
void main() {
    DerelictSFML2System.load();
    DerelictSFML2Window.load();
}
```

Alternatively, you can import them all at once with a single line, then all five loaders will be available:

```d
import derelict.sfml2;
void main() {
    DerelictSFML2Audio.load();
    DerelictSFML2Graphics.load();
    DerelictSFML2Network.load();
    DerelictSFML2System.load();
    DerelictSFML2Window.load();
}
```

!!! note
    All of the SFML libraries depend on the System library, but that **does not** mean you have to load the System library. On Windows, it needs to be in the same directory as the other libraries (on other systems it will generally be in the proper location) for them to find it, but you only need to load it yourself if you intend to call any functions from it.