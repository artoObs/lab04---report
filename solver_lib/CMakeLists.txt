cmake_minimum_required(VERSION 3.4)
project(solver_lib)

add_library(solver_lib STATIC solver.cpp)
target_include_directories(solver_lib PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
