cmake_minimum_required(VERSION 3.20)

project(Programs
    VERSION 1.0
    LANGUAGES CXX
)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_executable(hello src/helloWorld.cpp)

add_executable(add src/addNumbers.cpp)

add_executable(sqrt src/squareRoot.cpp)

add_executable(sum src/sumOfN.cpp)
