All of the Derelict packages are configured for convenient compilation via [DUB], the build and package tool for D. There are two ways to go about using DUB to compile any Derelict package for your projects. The recommended way is to [use DUB to manage] your own project. This is easiest and requires little effort to configure. If you [do not use DUB to manage] your project, you will still need it to compile the Derelict libraries you want to use. You can then configure your preferred build system or IDE to find the Derelict source files and libraries when compiling and linking your project.

[DUB]: https://code.dlang.org/download
[use DUB to manage]: with-dub
[do not use DUB to manage]: without-dub