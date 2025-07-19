cmake_minimum_required(VERSION 2.6)

project(test_ring_buffer CXX)

option(OPTION_SANITISERS "Test with sanitisers" OFF)

add_definitions(-std=c++20)

set(CMAKE_CXX_FLAGS "-g1 -O2")
if(OPTION_SANITISERS)
	set(CMAKE_CXX_FLAGS "-g1 -O2 -fno-omit-frame-pointer -fsanitize=address -fsanitize=undefined -fsanitize-address-use-after-scope -D_GLIBCXX_DEBUG -D_GLIBCXX_DEBUG_PEDANTIC -fstack-protector")
	link_libraries("-fsanitize=address -fsanitize=undefined")
endif()

enable_testing()
add_executable(test_ring_buffer test_main.cpp test_ring_buffer.cpp ../ring_buffer.hpp ../3rdparty/catch2/catch.hpp)

include(../3rdparty/catch2/Catch.cmake)
catch_discover_tests(test_ring_buffer)
