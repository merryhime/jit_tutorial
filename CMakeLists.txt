cmake_minimum_required(VERSION 3.12 FATAL_ERROR)

project(jit_tutorial LANGUAGES CXX)

# Project options
option(JT_WARNINGS_AS_ERRORS "Warnings as errors" ON)

# Default to a Release build
if (NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE "Release" CACHE STRING "Choose the type of build, options are: Debug Release RelWithDebInfo MinSizeRel." FORCE)
    message(STATUS "Defaulting to a Release build")
endif()

# Warn on CMake API deprecations
set(CMAKE_WARN_DEPRECATED ON)

# Disable in-source builds
set(CMAKE_DISABLE_SOURCE_CHANGES ON)
set(CMAKE_DISABLE_IN_SOURCE_BUILD ON)
if ("${CMAKE_SOURCE_DIR}" STREQUAL "${CMAKE_BINARY_DIR}")
    message(SEND_ERROR "In-source builds are not allowed.")
endif()

# Add the module directory to the list of paths
list(APPEND CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/CMakeModules")

# Compiler flags
if (MSVC)
    set(JT_CXX_FLAGS
        /std:c++latest
        /experimental:external
        /external:W0
        /external:anglebrackets
        /W4
        /permissive- # Stricter C++ standards conformance
        /MP
        /Zi
        /Zo
        /EHsc
        /Zc:externConstexpr # Allows external linkage for variables declared "extern constexpr", as the standard permits.
        /Zc:inline          # Omits inline functions from object-file output.
        /Zc:throwingNew     # Assumes new (without std::nothrow) never returns null.
        /volatile:iso       # Use strict standard-abiding volatile semantics
        /bigobj             # Increase number of sections in .obj files
        /DNOMINMAX)

    if (JT_WARNINGS_AS_ERRORS)
        list(APPEND JT_CXX_FLAGS /WX)
    endif()

    if (CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
        list(APPEND JT_CXX_FLAGS
             -Qunused-arguments
             -Wno-missing-braces)
    endif()
else()
    set(JT_CXX_FLAGS
        -Wall
        -Wextra
        -Wcast-qual
        -pedantic
        -pedantic-errors
        -Wfatal-errors
        -Wno-missing-braces)

    if (JT_WARNINGS_AS_ERRORS)
        list(APPEND JT_CXX_FLAGS -Werror)
    endif()
endif()

# Dependencies
find_package(Catch2 3 CONFIG REQUIRED)
find_package(fmt 9 CONFIG REQUIRED)

# Project files
enable_testing(true)
add_subdirectory(src)
