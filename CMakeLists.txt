cmake_minimum_required(VERSION 3.10)
project(solver)

set(SOLVER_VERSION_MAJOR 1)
set(SOLVER_VERSION_MINOR 0)
set(SOLVER_VERSION_PATCH 0)
set(SOLVER_VERSION
${SOLVER_VERSION_MAJOR}.${SOLVER_VERSION_MINOR}.${SOLVER_VERSION_PATCH}.${SOLVER_VERSION_TWEAK})
set(SOLVER_VERSION_STRING "v${SOLVER_VERSION}")

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_library(formatter STATIC formatter_lib/formatter.cpp)
include_directories(formatter_lib)
install(TARGETS formatter
    EXPORT print-config
    ARCHIVE DESTINATION lib
    LIBRARY DESTINATION lib
)
add_library(formatter_ex STATIC formatter_ex_lib/formatter_ex.cpp)
include_directories(formatter_ex_lib)
target_link_libraries(formatter_ex formatter)

add_executable(hello_world hello_world_application/hello_world.cpp)
target_link_libraries(hello_world formatter formatter_ex)

add_library(solver_lib STATIC solver_lib/solver.cpp)
include_directories(solver_lib)

add_executable(solver solver_application/equation.cpp)
target_link_libraries(solver formatter formatter_ex solver_lib)
install(TARGETS solver DESTINATION bin)
include(CPackConfig.cmake)
