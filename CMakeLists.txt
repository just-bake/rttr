cmake_minimum_required (VERSION 3.0)

project ("rttr" LANGUAGES CXX)

set(CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/CMake")

if (CMAKE_BUILD_TYPE STREQUAL "")
  # CMake defaults to leaving CMAKE_BUILD_TYPE empty. This screws up
  # differentiation between debug and release builds.
  set(CMAKE_BUILD_TYPE "RelWithDebInfo" CACHE STRING "Choose the type of build, options are: None (CMAKE_CXX_FLAGS or CMAKE_C_FLAGS used) Debug Release RelWithDebInfo MinSizeRel." FORCE)
endif ()

# our little cmake helper functions
include(utility)

# set up option variable for cmake
option(BUILD_RTTR_DYNAMIC "Build the dynamic/shared version of RTTR library" ON)
option(BUILD_STATIC "Build RTTR as static library" OFF)
option(BUILD_WITH_STATIC_RUNTIME_LIBS "Link against the static runtime libraries" OFF)
option(BUILD_WITH_RTTI "Enable build with C++ runtime type information for compilation" ON)
option(USE_PCH "Use precompiled header files for compilation" ON)

# one precompiled headers cannot be used for multiple ninja targets
# thats why we have to disable this option, when BUILD_STATIC or
# BUILD_WITH_STATIC_RUNTIME_LIBS is ON (every target will create the same PCH.pch file)
# to get it working, we need the feature to enable different source properties
# for different targets
if (USE_PCH)
  if (BUILD_STATIC OR BUILD_WITH_STATIC_RUNTIME_LIBS)
    set(USE_PCH FALSE)
  endif()
endif()

include(config)
include(3rd_party_libs)

# here we add our source code
add_subdirectory(src)
