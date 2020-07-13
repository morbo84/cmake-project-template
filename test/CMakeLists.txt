#
# project's tests configuration
#

set(COMMON_GTEST_LIBRARIES
    gtest_main
    gmock
)

#
# Search recursively within this folder searching for headers
#

file(GLOB_RECURSE PROJECT_TESTCASES_HEADERS *.hpp *.h)

#
# Unit test executable
#

set(PROJECT_TEST_EXECUTABLE ${PROJECT_NAME}-unit-test)

set(PROJECT_TESTCASES_SOURCES
    # sources: add .cpp test sources here below...
)

add_executable(
    ${PROJECT_TEST_EXECUTABLE}
    $<TARGET_OBJECTS:${COMMON_OBJS_TARGET}>
    ${PROJECT_TESTCASES_HEADERS}
    ${PROJECT_TESTCASES_SOURCES}
    UnitTestMain.cpp
)

target_include_directories(
    ${PROJECT_TEST_EXECUTABLE}
    PRIVATE ${GOOGLETEST_DEPS_DIR}/${SUBMODULE_SOURCE_DIR_NAME}/googletest/include
    PRIVATE ${GOOGLETEST_DEPS_DIR}/${SUBMODULE_SOURCE_DIR_NAME}/googlemock/include
    PRIVATE ${PROJECT_SOURCE_DIR}/src
)

target_link_libraries(
    ${PROJECT_TEST_EXECUTABLE}
    PRIVATE ${CMAKE_THREAD_LIBS_INIT}
    PRIVATE ${ZLIB_LIBRARIES}
    PRIVATE ${COMMON_GTEST_LIBRARIES}
)

# The unit test executable will run when you launch the `make test` command
add_test(NAME ${PROJECT_TEST_EXECUTABLE} COMMAND ${PROJECT_TEST_EXECUTABLE})
