libpq library build for Staticlibs
==================================

[![travis](https://travis-ci.org/staticlibs/external_libpq.svg?branch=master)](https://travis-ci.org/staticlibs/external_libpq)
[![appveyor](https://ci.appveyor.com/api/projects/status/github/staticlibs/external_libpq?svg=true)](https://ci.appveyor.com/project/staticlibs/external-libpq)

This project is a part of [Staticlibs](http://staticlibs.net/).

This project contains a CMake build file for building the [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) library that 
can be used to build sources imported from [CentOS lookaside](https://github.com/staticlibs/lookaside_postgresql.git).

How to build
------------

[CMake](http://cmake.org/) is required for building.

To build the library on Windows using Visual Studio 2013 Express run the following commands using
Visual Studio development command prompt 
(`C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\Tools\Shortcuts\VS2013 x86 Native Tools Command Prompt`):

    git clone https://github.com/staticlibs/external_libpq.git
    git clone https://github.com/staticlibs/lookaside_postgresql.git
    cd external_libpq
    mkdir build
    cd build
    cmake ..
    msbuild external_libpq.sln

See [StaticlibsToolchains](https://github.com/staticlibs/wiki/wiki/StaticlibsToolchains) for 
more information about the CMake toolchains setup and cross-compilation.

License information
-------------------

This project is released under the [Apache License 2.0](http://www.apache.org/licenses/LICENSE-2.0).

Changelog
---------

**2018-02-28**

 * version 9.6.3-3
 * drop submodule

**2017-12-31**

 * version 9.6.3-2
 * vs2017 support

**2017-07-04**

 * version 9.6.3-2
 * initial version
