cmake_minimum_required(VERSION 3.1)
cmake_policy(SET CMP0074 NEW)

project (GL C CXX)

enable_testing()

# Compile C++ files with C++17
set(CMAKE_CXX_STANDARD 17)

# Compile C files with C11
set(CMAKE_C_STANDARD 11)

# Use clang-tidy if available 
find_program(CLANG_TIDY "clang-tidy")

if(CLANG_TIDY)
    message(STATUS "Found clang-tidy")
    set(CMAKE_CXX_CLANG_TIDY 
        clang-tidy 
        -warnings-as-errors=* 
        -checks=-*,readability-*,-readability-magic-numbers,-readability-isolate-declaration
    )
    set(CMAKE_C_CLANG_TIDY 
        clang-tidy 
        -warnings-as-errors=* 
        -checks=-*,readability-*,-readability-magic-numbers,-readability-isolate-declaration
    )
endif()

# Search for Boost static libraries only
set(Boost_USE_STATIC_LIBS ON)

find_package(Boost 1.65.0 COMPONENTS unit_test_framework QUIET)

if(Boost_FOUND)
    include_directories(${Boost_INCLUDE_DIR})
endif()

# Specify where to put all the RUNTIME target files when built.
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

# append the d suffix for debug libraries
set(CMAKE_DEBUG_POSTFIX d)

# Add include directories to the build.
include_directories (include)

# Copy user libraries into the build folder
if(EXISTS "${CMAKE_SOURCE_DIR}/lib" AND IS_DIRECTORY "${CMAKE_SOURCE_DIR}/lib")
    file(GLOB_RECURSE libraries
        "lib/*${CMAKE_SHARED_LIBRARY_SUFFIX}"
        "lib/*${CMAKE_STATIC_LIBRARY_SUFFIX}"
    )

    foreach(library IN LISTS libraries)
        file(COPY ${library} DESTINATION ${CMAKE_LIBRARY_OUTPUT_DIRECTORY})
    endforeach()

    file(GLOB_RECURSE libraries
        "${CMAKE_LIBRARY_OUTPUT_DIRECTORY}/*${CMAKE_SHARED_LIBRARY_SUFFIX}"
        "${CMAKE_LIBRARY_OUTPUT_DIRECTORY}/*${CMAKE_STATIC_LIBRARY_SUFFIX}"
    )
endif()

# Copy user binaries into the build folder
if(EXISTS "${CMAKE_SOURCE_DIR}/bin" AND IS_DIRECTORY "${CMAKE_SOURCE_DIR}/bin")
    file(GLOB_RECURSE dlls
        "bin/*${CMAKE_SHARED_LIBRARY_SUFFIX}"
    )

    foreach(dll IN LISTS dlls)
        file(COPY ${dll} DESTINATION ${CMAKE_RUNTIME_OUTPUT_DIRECTORY})
    endforeach()
endif()

# Recurse into the "src" subdirectory. This does not actually
# cause another cmake executable to run. The same process will walk through
# the project's entire directory structure.
add_subdirectory (src)
