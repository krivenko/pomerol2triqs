# Version number of the application
set(POMEROL2TRIQS_VERSION "0.5")

# Start configuration
cmake_minimum_required(VERSION 3.8 FATAL_ERROR)
cmake_policy(VERSION 3.8)
if(POLICY CMP0074)
  cmake_policy(SET CMP0074 NEW)
endif()
project(pomerol2triqs CXX)
set(CMAKE_CXX_STANDARD 17)

# We use shared libraries
option(BUILD_SHARED_LIBS "Build shared libraries" ON)

# Load TRIQS and Cpp2Py
find_package(TRIQS 3.0.0 REQUIRED)

# Append TRIQS installed files to the cmake load path
list(APPEND CMAKE_MODULE_PATH ${TRIQS_ROOT}/share/cmake)
# Append Pomerol installed files to the cmake load path
list(APPEND CMAKE_MODULE_PATH ${POMEROL_PATH}/share/pomerol/cmake)

# Build the optimized version by default
if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE Release CACHE STRING "Choose the type of build" FORCE)
endif()
message(STATUS "CMAKE_BUILD_TYPE: ${CMAKE_BUILD_TYPE}")

# Default install directory to TRIQS_ROOT if not given
if(CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT OR (NOT IS_ABSOLUTE ${CMAKE_INSTALL_PREFIX}))
  message(STATUS "No install prefix given (or invalid). Defaulting to TRIQS_ROOT")
  set(CMAKE_INSTALL_PREFIX ${TRIQS_ROOT} CACHE PATH "Install path prefix, prepended onto install directories." FORCE)
endif()
message(STATUS "CMAKE_INSTALL_PREFIX: ${CMAKE_INSTALL_PREFIX}")

# Python Support
option(PythonSupport "Build with Python support" ON)
if(PythonSupport AND NOT TRIQS_WITH_PYTHON_SUPPORT)
  message(FATAL_ERROR "TRIQS was installed without Python support. Cannot build the Python Interface. Disable the build with -DPythonSupport=OFF")
endif()

# Load Pomerol
find_package(pomerol REQUIRED)

# Find MPI
find_package(MPI)

# Get git hash
triqs_get_git_hash_of_source_dir(POMEROL2TRIQS_GIT_HASH)
message(STATUS "Git hash: ${POMEROL2TRIQS_GIT_HASH}")

option(Tests "Enable Tests" ON)

# Compile options
include_directories(c++)

triqs_set_rpath_for_all_targets()

# Compile C++ code
add_subdirectory(c++)

# Python interface
if(PythonSupport)
  add_subdirectory(python)
endif()

# Testing
if(${Tests})
  enable_testing()
  add_subdirectory(test)
endif()

# Additional configuration files
add_subdirectory(share)

