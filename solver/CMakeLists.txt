cmake_minimum_required(VERSION 3.4)
project(hello_world)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_subdirectory(../formatter_ex_lib formatter_ex_lib)
add_subdirectory(../solver_lib solver_lib)

add_executable(solver main.cpp)

target_link_libraries(solver PRIVATE formatter_ex solver_lib)
