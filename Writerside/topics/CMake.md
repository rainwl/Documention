# CMake
All content should be viewed as targets and dependencies.

## CMakeLists.txt basics

```CMake
cmake_minimum_required(VERSION 3.27)
project(
        CMakeLearn
        LANGUAGES CXX C
        DESCRIPTION "a test project"
        VERSION 0.0.1
)
set(CMAKE_CXX_STANDARD 20)
add_executable(CMakeLearn src/main.cpp
        include/engine.h)
target_include_directories(CMakeLearn PRIVATE include)
```

We should create a `CMakeLists.txt` under the project directory,
if you use `CLion`,it will be generated automatically.

The project name is `required`,other items are `optional`.

The `modifier` `PUBLIC` means deep search the path,but `PRIVATE` is not.

## Link library(eg:absl)

We should create a directory for libs,and add a `CMakeLists.txt` in it.

```CMake
add_subdirectory() # if 3rdlibs contains a proj made with cmakelists,just use this to add it .
```

In our main `CMakeLists.txt`,add this to our project also.

```CMake
target_link_libraries(CMakeLearn PUBLIC $3rdlibs's project name$)#link 3rdlibs to our project
```

### example

For example ,we want to use `absl`.Create a directory named `3rdlibs` under the root dir.

And create a `CMakeLists.txt` under `3rdlibs`.(Because absl already has CMakeLists.txt,
so we do not need to create a new one)

```CMake
add_subdirectory(abseil-cpp)
target_link_libraries(CMakeLearn PUBLIC absl)
```

**Complete CMakeLists.txt contents**

```CMake
cmake_minimum_required(VERSION 3.27)
project(CMakeLearn)
set(CMAKE_CXX_STANDARD 20)
add_subdirectory(3rdlibs)
add_library(CMakeLearn STATIC src/main.cpp)
target_include_directories(CMakeLearn PUBLIC include)
target_link_libraries(CMakeLearn PUBLIC absl)
```

The `add_subdirectory` add the `3rdlibs`,and the `target_link_libraries` link the `absl`,the name `absl` can be found in
the CMakeLists.txt in abseil-cpp

![](CMake1.png){width="300"}

![](CMake2.png){width="300"}

## Link .h library(eg:Eigen)

For example,`Eigen`,we put it in directory `3rdlibs`.

Create a `CMakeLists.txt` in `Eigen`,like following:

```CMake
add_library(Eigen INTERFACE)
target_include_directories(Eigen INTERFACE ./)
target_compile_features(Eigen INTERFACE cxx_std_20)
```

And in `3rdlibs` 's `CMakeLists.txt`,add following:

```CMake
add_subdirectory(Eigen)
```

And in main `CMakeLists.txt`,add following:

```CMake
target_link_libraries(CMakeLearn PUBLIC Eigen)
```

Up to now ,we take a look at main `CMakeLists.txt`.

```CMake
cmake_minimum_required(VERSION 3.27)
project(CMakeLearn)

set(CMAKE_CXX_STANDARD 20)

add_subdirectory(3rdlibs)
add_library(CMakeLearn STATIC src/main.cpp)

target_include_directories(CMakeLearn PUBLIC include)
target_compile_features(CMakeLearn PRIVATE cxx_std_20)
#target_link_libraries(CMakeLearn PUBLIC absl)
target_link_libraries(CMakeLearn PUBLIC Eigen)
```

## Link .dll(eg:mono)

For example:`mono`,and in `3rdlibs`:

```CMake
add_subdirectory(Eigen)
add_subdirectory(mono)
```

In `mono`:

```CMake
set(MONO_PATH "" CACHE PATH "the C# mono path")
message("your mono path is ${MONO_PATH}")
add_library(mono SHARED IMPORTED GLOBAL)

set(mono_dll "${MONO_PATH}/bin/mono-2.0-sgen.dll" CACHE INTERNAL "the mono dll path")
target_include_directories(mono INTERFACE "${MONO_PATH}/include/mono-2.0")
set_target_properties(mono PROPERTIES
        IMPORTED_LOCATION "${MONO_PATH}/bin/mono-2.0-sgen.dll"
        IMPORTED_IMPLIB "${MONO_PATH}/lib/mono-2.0-sgen.lib"
)
```

If other dlls,we create a directory named such as `temp`,and add a `find_temp.cmake`,and add it to `3rdlibs`.

```CMake
#add_subdirectory(abseil-cpp)
add_subdirectory(Eigen)

add_subdirectory(mono)
include(temp/fine_temp.cmake)
```

## Package all *.cpp

If we have lots of .cpp files,we can use `aux_source_directory` to package them all.

```CMake
aux_source_directory(./src engine_src)
add_library(CMakeLearn STATIC
        ${engine_src}
)
```

## Package all *.h

```CMake
file(GLOB engine_header_files ./include/core/*.h)
message("header files: ${engine_header_files}")
```

**or**

```CMake
file(GLOB_RECURSE engine_header_all_files ./include/*.h)
```

Up to now ,our main `CMakeLists.txt` is as follows:

```CMake
cmake_minimum_required(VERSION 3.27)
project(CMakeLearn)
#set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

set(CMAKE_CXX_STANDARD 20)
set(engine_name CMakeLearn)
#set(xx 123 CACHE)
#set(XX_PATH "" CACHE PATH)

add_subdirectory(3rdlibs)
aux_source_directory(./src engine_src)
message("source files: ${engine_src}")
foreach (var ${engine_src})
    message(${var})
endforeach ()

file(GLOB engine_header_files ./include/core/*.h)
message("header files: ${engine_header_files}")

add_library(CMakeLearn STATIC
        ${engine_src}
        ${engine_header_files}
)
target_include_directories(CMakeLearn PUBLIC include)
target_compile_features(CMakeLearn PRIVATE cxx_std_20)
target_precompile_headers(CMakeLearn PUBLIC ./include/pch.h)
#target_link_libraries(CMakeLearn PUBLIC absl)
target_link_libraries(CMakeLearn PUBLIC Eigen)

option(ENGINE_BUILD_TEST "should build unit test" OFF)

if (PROJECT_IS_TOP_LEVEL OR ENGINE_BUILD_TEST)
    include(CTest)
    enable_testing()
    add_subdirectory(unittest)
endif ()
```

## Precompile headers

We create a `PCH.h` in directory `include`

```C++
#ifndef CMAKELEARN_PCH_H
#define CMAKELEARN_PCH_H

#endif //CMAKELEARN_PCH_H
#pragma once

#include "Eigen"
#include <iostream>

//etc include files
```

In main `CMakeLists.txt`:

```CMake
target_precompile_headers(CMakeLearn PUBLIC ./include/pch.h)
```

In `unittest\CMakeLists.txt`

```CMake
target_precompile_headers(cgmath REUSE_FROM CMakeLearn)
```

## UnitTest

```CMake
include(CTest)
enable_testing()
add_subdirectory(unittest)
```

We should create a directory such as named `unittest`,and put a `CMakeLists.txt` here also.

**unittest\CMakeLists.txt**

```CMake
add_executable(cgmath ./cgmath.cpp)
add_test(NAME cgmath COMMAND $<TARGET_FILE:cgmath>)
target_link_libraries(cgmath PRIVATE CMakeLearn)
```

> To create dependencies between targets,use `target_link_libraries`.

And we should know that CMake is built based on `target`.

## Command basics

| **Function**        | **Command**                         |
|---------------------|-------------------------------------|
| **Compile command** | cmake -S . -B cmake-build           |
| **Open sln**        | start .\cmake-build\CMakeLearn.sln  |
| **Project type**    | cmake -G                            |
| **Generate ninja**  | cmake -S . -B cmake-build -G"Ninja" |
| **Build**           | cmake --build .\cmake-build\        |
| **Run**             | .\cmake-build\Debug\CMakeLearn.exe  |

**Compile command**

```Bash
cmake -S . -B cmake-build
```

Meaning
: `S` stands for `source` and specifies the location of `CMakeLists.txt`.
: `B` stands for `build`
: `cmake-build` is the build directory name.

`output`

```Bash
PS C:\Users\22153\Documents\Local Projects\CMakeLearn> cmake -S . -B cmake-build
-- Building for: Visual Studio 17 2022
-- The CXX compiler identification is MSVC 19.37.32822.0
-- The C compiler identification is MSVC 19.37.32822.0
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: C:/Program Files/Microsoft Visual Studio/2022/Community/VC/Tools/MSVC/14.37.32822/bin/Hostx64/x64/cl.exe - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: C:/Program Files/Microsoft Visual Studio/2022/Community/VC/Tools/MSVC/14.37.32822/bin/Hostx64/x64/cl.exe - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Configuring done (2.6s)
-- Generating done (0.0s)
-- Build files have been written to: C:/Users/22153/Documents/Local Projects/CMakeLearn/cmake-build
```

We should know that `CMake` is a project generator,not a compiler.

**Open sln**

And we can use this command to open the sln.

```Bash
start .\cmake-build\CMakeLearn.sln
```

`output`

```Bash
PS C:\Users\22153\Documents\Local Projects\CMakeLearn> start .\cmake-build\CMakeLearn.sln
```

**Generate different project type**

we can use the following command in terminal to look all types we can generate.

```Bash
cmake -G
```

`output`

```Bash
PS C:\Users\22153\Documents\Local Projects\CMakeLearn> cmake -G
CMake Error: No generator specified for -G

Generators
* Visual Studio 17 2022        = Generates Visual Studio 2022 project files.
                                 Use -A option to specify architecture.
  Visual Studio 16 2019        = Generates Visual Studio 2019 project files.
                                 Use -A option to specify architecture.
  Visual Studio 15 2017 [arch] = Generates Visual Studio 2017 project files.
                                 Optional [arch] can be "Win64" or "ARM".
  Visual Studio 14 2015 [arch] = Generates Visual Studio 2015 project files.
                                 Optional [arch] can be "Win64" or "ARM".
  Visual Studio 12 2013 [arch] = Generates Visual Studio 2013 project files.
                                 Optional [arch] can be "Win64" or "ARM".
  Visual Studio 11 2012 [arch] = Deprecated.  Generates Visual Studio 2012
                                 project files.  Optional [arch] can be
                                 "Win64" or "ARM".
  Visual Studio 9 2008 [arch]  = Deprecated.  Generates Visual Studio 2008
                                 project files.  Optional [arch] can be
                                 "Win64" or "IA64".
  Borland Makefiles            = Generates Borland makefiles.
  NMake Makefiles              = Generates NMake makefiles.
  NMake Makefiles JOM          = Generates JOM makefiles.
  MSYS Makefiles               = Generates MSYS makefiles.
  MinGW Makefiles              = Generates a make file for use with
                                 mingw32-make.
  Green Hills MULTI            = Generates Green Hills MULTI files
                                 (experimental, work-in-progress).
  Unix Makefiles               = Generates standard UNIX makefiles.
  Ninja                        = Generates build.ninja files.
  Ninja Multi-Config           = Generates build-<Config>.ninja files.
  Watcom WMake                 = Generates Watcom WMake makefiles.
  CodeBlocks - MinGW Makefiles = Generates CodeBlocks project files
                                 (deprecated).
  CodeBlocks - NMake Makefiles = Generates CodeBlocks project files
                                 (deprecated).
  CodeBlocks - NMake Makefiles JOM
                               = Generates CodeBlocks project files
                                 (deprecated).
  CodeBlocks - Ninja           = Generates CodeBlocks project files
                                 (deprecated).
  CodeBlocks - Unix Makefiles  = Generates CodeBlocks project files
                                 (deprecated).
  CodeLite - MinGW Makefiles   = Generates CodeLite project files
                                 (deprecated).
  CodeLite - NMake Makefiles   = Generates CodeLite project files
                                 (deprecated).
  CodeLite - Ninja             = Generates CodeLite project files
                                 (deprecated).
  CodeLite - Unix Makefiles    = Generates CodeLite project files
                                 (deprecated).
  Eclipse CDT4 - NMake Makefiles
                               = Generates Eclipse CDT 4.0 project files
                                 (deprecated).
  Eclipse CDT4 - MinGW Makefiles
                               = Generates Eclipse CDT 4.0 project files
                                 (deprecated).
  Eclipse CDT4 - Ninja         = Generates Eclipse CDT 4.0 project files
                                 (deprecated).
  Eclipse CDT4 - Unix Makefiles= Generates Eclipse CDT 4.0 project files
                                 (deprecated).
  Kate - MinGW Makefiles       = Generates Kate project files (deprecated).
  Kate - NMake Makefiles       = Generates Kate project files (deprecated).
  Kate - Ninja                 = Generates Kate project files (deprecated).
  Kate - Ninja Multi-Config    = Generates Kate project files (deprecated).
  Kate - Unix Makefiles        = Generates Kate project files (deprecated).
  Sublime Text 2 - MinGW Makefiles
                               = Generates Sublime Text 2 project files
                                 (deprecated).
  Sublime Text 2 - NMake Makefiles
                               = Generates Sublime Text 2 project files
                                 (deprecated).
  Sublime Text 2 - Ninja       = Generates Sublime Text 2 project files
                                 (deprecated).
  Sublime Text 2 - Unix Makefiles
                               = Generates Sublime Text 2 project files
                                 (deprecated).
```

The front has a star means it is the default type.

Such as `ninja`

```Bash
cmake -S . -B cmake-build -G"Ninja"
```

**Compile project**

```Bash
cd .\cmake-build\
cd ..
```

```Bash
cmake --build .\cmake-build\
```

`output`

```Bash
PS C:\Users\22153\Documents\Local Projects\CMakeLearn> cmake --build .\cmake-build\
MSBuild version 17.7.2+d6990bcfa for .NET Framework

1>Checking Build System
Building Custom Rule C:/Users/22153/Documents/Local Projects/CMakeLearn/CMakeLists.txt
main.cpp
CMakeLearn.vcxproj -> C:\Users\22153\Documents\Local Projects\CMakeLearn\cmake-build\Debug\CMakeLearn.exe
Building Custom Rule C:/Users/22153/Documents/Local Projects/CMakeLearn/CMakeLists.txt
```

**Run**

and we can use this to run the exe

```Bash
PS C:\Users\22153\Documents\Local Projects\CMakeLearn> .\cmake-build\Debug\CMakeLearn.exe
Hello, World!
```

and the `ALL_BUILD` ,the `ZERO_CHECK` is the VS only has.

if we use ninja or makefile,we need to add a command in CMakeLists.txt

```CMake
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
```

and if we want to specify compile version

```CMake
target_compile_features(CMakeLearn PRIVATE cxx_std_20)
```

## Reuse syntax

In `unittest\CMakeLists.txt`

```CMake
#define AddTest(x)
macro(AddTest target_name)
    add_executable(${target_name} ./${target_name}.cpp)
    add_test(NAME ${target_name} COMMAND $<TARGET_FILE:${target_name}>)
    target_link_libraries(${target_name} PRIVATE CMakeLearn)
    target_precompile_headers(${target_name} REUSE_FROM CMakeLearn)
endmacro()

AddTest(cgmath)
# AddTest(geom)

# add_executable(cgmath ./cgmath.cpp)
# add_test(NAME cgmath COMMAND $<TARGET_FILE:cgmath>)
# target_link_libraries(cgmath PRIVATE CMakeLearn)
# target_precompile_headers(cgmath REUSE_FROM CMakeLearn)
```

## Compile ON & OFF

Allow user to choose whether something compile or not .

In main `CMakeLists.txt`

```CMake
option(ENGINE_BUILD_TEST "should build unit test" OFF)

# include(CTest)
# enable_testing()
# add_subdirectory(unittest)

if (ENGINE_BUILD_TEST)
    include(CTest)
    enable_testing()
    add_subdirectory(unittest)
endif ()
```

After this,we can use `CMake GUI` to control whether compile this **ON or OFF**.

We can also use terminal:

```Bash
cmake -S . -B .\cmake-build\ -DENGINE_BUILD_TEST=ON
```

`output`

```Bash
PS C:\Users\22153\Documents\Local Projects\CMakeLearn> cmake -S . -B .\cmake-build\ -DENGINE_BUILD_TEST=OFF
-- Configuring done (0.0s)
-- Generating done (0.0s)
-- Build files have been written to: C:/Users/22153/Documents/Local Projects/CMakeLearn/cmake-build
```

## Set Command

```CMake
set(engine_name CMakeLearn)
```

In other locations,we can use `${engine_name}` to replace name of this target.

## CACHE

```CMake
set(xx 123 CACHE)
set(XX_PATH "" CACHE PATH "path of XX")
```

If we use cache,data can be persisted in the GUI.

## TOP_LEVEL

If other users do not want to run our test,we could do this.

```CMake
if (PROJECT_IS_TOP_LEVEL OR ENGINE_BUILD_TEST)
    include(CTest)
    enable_testing()
    add_subdirectory(unittest)
endif ()
```

## Print message

```CMake
message("source files: ${engine_src}")
```

Foreach syntax:

```CMake
aux_source_directory(./src engine_src)

message("source files: ${engine_src}")

foreach (var in ${engine_src})
    message(${var})
endforeach ()
```

**or**

```CMake
foreach (var ${engine_src})
    message(${var})
endforeach ()
```

## Sandbox

We could create a directory named `sandbox` under the root,and create a `main.cpp` and a
`CMakeLists.txt` under it.

```CMake
add_executable(sandbox main.cpp)
target_link_libraries(sandbox PRIVATE ${engine_name})
add_custom_command(TARGET sandbox
        POST_BUILD
        COMMAND ${CMAKE_COMMAND} -E copy_if_different ${mono_dll} $<TARGET_FILE_DIR:sandbox>
)
```

In root directory ,our main `CMakeLists.txt`.

```CMake
add_subdirectory(sandbox)
```

## CMake communicate with *.cpp

For example,in `mono\CMakeLists.txt`

```CMake
target_compile_definitions(mono INTERFACE MONO_PATH = "${MONO_PATH}")
```

It just like

```C++
#define MONO_PATH
```

We can use it directly in `*.cpp`.