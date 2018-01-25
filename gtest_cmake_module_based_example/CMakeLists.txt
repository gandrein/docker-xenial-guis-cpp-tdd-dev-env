cmake_minimum_required(VERSION 2.8.2)

project(DP_Example)
include(CTest)

# Include gtest as a CMake Module
list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake/Modules")
include(gtest)

# Trivial example using gtest and gmock
add_executable(example example.cpp)
target_link_libraries(example gtest gmock_main)
add_test(NAME example_test COMMAND example)
