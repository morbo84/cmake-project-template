#
# C++ project template for CMake
#

cmake_minimum_required(VERSION 3.12.4)

#
# General configuration
#

if(UNIX AND NOT APPLE)
    set(LINUX TRUE)
endif(UNIX AND NOT APPLE)

#
# Project configuration
#

project(set_your_project_name VERSION 0.0.1 LANGUAGES C CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
if (MSVC)
	add_compile_definitions(_USE_MATH_DEFINES)
	set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} /Od /DEBUG /W3")
	set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} /O2")
else(MSVC)
	set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -O0 -g -Wall")
	set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -O3")
endif(MSVC)

# Set Debug as the fallback build type
if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Debug)
endif()

option(ASAN "Enable address sanitizer" OFF)

if(ASAN)
    message("-- Address sanitizer enabled")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fsanitize=address -fno-omit-frame-pointer")
endif(ASAN)

# Ignore clang warnings about silly class / struct mismatches
if (CMAKE_CXX_COMPILER_ID MATCHES "Clang")
    set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -Wno-mismatched-tags")
    set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -Wno-mismatched-tags")
endif()


#
# Referenced packages
#

set(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)
set(THREADS_PREFER_PTHREAD_FLAG ON)
set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(SUBMODULE_SOURCE_DIR_NAME repo)

find_package(Threads REQUIRED)
find_package(ZLIB REQUIRED)


#
# Add the source directory to the tree
#

# This target will be a _cmake object library_ that contains all source object files;
# both the main project's executable and every test executable will link against it,
# without requiring a recompilation of the same files for every target.
set(COMMON_OBJS_TARGET common_objects)

add_subdirectory(src)


#
# Enable test support using ctest-like interface
#

option(BUILD_TESTS "Enable testing with ctest." OFF)

if(BUILD_TESTS)
    message("\n-- Building External Project: googletest")

    # gtest, gtest_main, gmock and gmock_main targets are available from now on
    set(GOOGLETEST_DEPS_DIR ${PROJECT_SOURCE_DIR}/deps/googletest)
    configure_file(cmake/in/googletest.in ${GOOGLETEST_DEPS_DIR}/CMakeLists.txt)
    execute_process(COMMAND ${CMAKE_COMMAND} -G "${CMAKE_GENERATOR}" . WORKING_DIRECTORY ${GOOGLETEST_DEPS_DIR})
    execute_process(COMMAND ${CMAKE_COMMAND} --build . WORKING_DIRECTORY ${GOOGLETEST_DEPS_DIR})
    set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)
    add_subdirectory(${GOOGLETEST_DEPS_DIR}/${SUBMODULE_SOURCE_DIR_NAME})

    message("-- Built External Project: googletest\n")

    enable_testing()

    set(UNIT_TESTS_DIR ${PROJECT_SOURCE_DIR}/test)
    add_subdirectory(${UNIT_TESTS_DIR})
endif()
