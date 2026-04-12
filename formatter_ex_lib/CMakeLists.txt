cmake_minimum_required(VERSION 3.4)
project(formatter_ex)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_subdirectory(../formatter_lib formatter_lib)

add_library(formatter_ex STATIC formatter_ex.cpp)

include_directories(${CMAKE_CURRENT_SOURCE_DIR})

target_link_libraries(formatter_ex PRIVATE formatter)
