All of the packages in Derelict are _dynamic bindings_. Dynamic bindings may not be suitable for every application. Sometimes, _static bindings_ may be more desirable. Before learning how to compile, link and run programs using Derelict, it is useful to understand the difference between the two types of bindings. The page [About Bindings] explains this along with other terminology used in this documentation that will be helpful when discussing Derelict with others.

Once the decision to use Derelict has been made, the next step is to decide how to manage the project. Derelict is designed to work out of the box with [DUB], the build tool and package manager for D. When using Derelict, it is most convenient to use DUB to build and manage the entire application, but it is not required. As long as DUB is available to build the Derelict libraries, they can be used with any build tool or IDE. The pages on [compiling and linking] explains the steps required for both cases. 

The last part of this section deals with how to use Derelict at runtime. All Derelict packages have a basic common interface that allow the shared libraries to which they bind to be easily loaded. This is covered in pages on [loading Derelict packages] and [load failures]. Some packages may provide additional features, such as the option to link with a static C library (in which case no loading is needed), or may require additional steps after the library is loaded. For details, see the package-specific documentation.

[About Bindings]: bindings
[DUB]: https://code.dlang.org/download
[compiling and linking]: building/overview
[loading Derelict packages]: loading/loader
[load failures]: loading/failure
