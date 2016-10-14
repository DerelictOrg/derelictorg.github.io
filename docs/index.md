## Overview

Derelict is a collection of dynamic bindings to C and C++ libraries for the D programming language. It enables users to access popular libraries such as OpenGL, SDL, Lua and more, easily and with little effort across several platforms. This set of documentation provides users with the necessary knowledge to get started with Derelict, including general information applicable to all packages in the collection and package-specific information for each package. It is recommended that new Derelict users read the general documentation first, followed by the specific documentation for packages they intend to use.

All Derelict packages make use of the same build and package management system, [DUB], and a common foundation in the form of DerelictUtil. The first place to go before starting with any Derelict package is the page on [Using Derelict]. There, you will learn the generalities of configuring your project to build and link with Derelict and about the common interface used to enable Derelict packages in your code.

While the steps to use most Derelict packages are identical, there are sometimes exceptions to the rule. Furthermore, each package has its own versioning scheme derived from the C or C++ library to which it binds. Because of this, before implementing Derelict in your project, you should take the time to read the specific documentation for any packages you intend to use. This way, you can be sure that you are basing your project on the version of the package appropriate to your requirements and will understand any special considerations necessary for that package.

## Support

In the past, forums were maintained for several years specifically for Derelict discussion and support. That is no longer the case. It is possible that a new forum will be opened at some point in the future, but for now the best methods to find help with Derelict are, in no particular order:

* Vist the IRC channel #D on freenode.net. A number of Derelict users are active there.
* Email Mike Parker via [aldacron@gmail.com].
* If you are new to D and Derelict, you might visit the [D Newsgroups], specifically `digitalmars.D.learn`, which can be accessed via Vladimir Panteleev's [web interface] and a [mailing list interface] in addition to standard newsreaders. However, please be aware that this is a generic D newsgroup and not specifically a Derelict support forum.
        
[DUB]: http://code.dlang.org/download  
[Using Derelict]: using-derelict/using
[D Newsgroups]: news://news.digitalmars.com
[web interface]: https://forum.dlang.org/
[mailing list interface]: http://lists.puremagic.com/mailman/listinfo
[aldacron@gmail.com]: mailto:aldacron@gmail.com   
