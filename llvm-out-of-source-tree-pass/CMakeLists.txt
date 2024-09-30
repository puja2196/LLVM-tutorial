cmake_minimum_required(VERSION 3.13)

# Set project name
project(PPProfilerPass VERSION 0.1 LANGUAGES CXX)

# Find the LLVM package
find_package(LLVM REQUIRED CONFIG)

# Print LLVM variables for debugging
message(STATUS "LLVM_LIBRARY_DIR: ${LLVM_LIBRARY_DIR}")
message(STATUS "LLVM_INCLUDE_DIRS: ${LLVM_INCLUDE_DIRS}")
message(STATUS "LLVM_DEFINITIONS: ${LLVM_DEFINITIONS}")

# Set up include directories for LLVM
include_directories(${LLVM_INCLUDE_DIRS})
add_definitions(${LLVM_DEFINITIONS})

# Define the pass plugin
add_library(PPProfilerPass SHARED PPProfiler.cpp)

# Link against LLVM libraries
target_link_libraries(PPProfilerPass PRIVATE
    ${LLVM_LIBRARY_DIR}/libLLVM.so
)

# Set LLVM's C++ standard for the project
set_target_properties(PPProfilerPass PROPERTIES
    CXX_STANDARD 17
    CXX_STANDARD_REQUIRED YES
    CXX_EXTENSIONS NO
)

# Enable verbose output during compilation
set(CMAKE_VERBOSE_MAKEFILE ON)

# Install the pass plugin in the current directory (for testing with opt)
install(TARGETS PPProfilerPass
    LIBRARY DESTINATION .)

