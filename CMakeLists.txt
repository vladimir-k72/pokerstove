cmake_minimum_required(VERSION 3.5)
set(CMAKE_OSX_ARCHITECTURES "arm64")

project(pokerstove)

# TODO: This flags only works for GCC compatible compilers (Clang, Intel).
# TODO: This, ideally, should be per library requirement,
#       but since all of them require C++11, the flag is set globally.
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++14")

# TODO: Remove the deprecated register keywords
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-deprecated-register")

# Set up gtest. This must be set up before any subdirectories are
# added which will use gtest.
add_subdirectory(src/ext/googletest)
include_directories(${GTEST_INCLUDE_DIRS})
link_directories(${GTEST_LIBS_DIR})
add_definitions("-fPIC")
enable_testing()

#
# set up boost
#
set(Boost_USE_STATIC_LIBS OFF)
set(Boost_USE_MULTITHREADED ON)
find_package(Boost CONFIG COMPONENTS program_options REQUIRED)
include_directories(${Boost_INCLUDE_DIRS})
link_directories(${Boost_LIBRARY_DIRS})
message(STATUS "Boost Include: ${Boost_INCLUDE_DIRS}")
message(STATUS "Boost Libraries: ${Boost_LIBRARIES}")

# Also search for includes in PROJECT_BINARY_DIR to find config.h.
include_directories("${PROJECT_BINARY_DIR}")
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)

# This controls whether or not to build the python bridge
if(BUILD_PYTHON)
    add_subdirectory(src/lib/python)
endif()

# add the actual code
include_directories(src/lib)
add_subdirectory(src/lib)
add_subdirectory(src/programs)

# Add make install functionality
install(TARGETS peval)
install(TARGETS penum)
install(TARGETS ps-colex)
install(TARGETS ps-eval)
install(TARGETS ps-lut)
