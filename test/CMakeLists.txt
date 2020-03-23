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
    $<TARGET_OBJECTS:${QHTTP_OBJS_TARGET}>
    $<TARGET_OBJECTS:${GTKLIB_OBJS_TARGET}>
    $<TARGET_OBJECTS:${QR_IMAGE_EMBEDDED_OBJS_TARGET}>
    $<TARGET_OBJECTS:${POLDER_FOLDER_OBJS_TARGET}>
    $<TARGET_OBJECTS:${PATHREADER_OBJS_TARGET}>
    $<TARGET_OBJECTS:${CLIPPER_OBJS_TARGET}>
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

add_custom_command(
    TARGET ${PROJECT_TEST_EXECUTABLE} POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_directory
    ${CMAKE_SOURCE_DIR}/test/data $<TARGET_FILE_DIR:${PROJECT_TEST_EXECUTABLE}>/data
)

# The unit test executable will run when you launch the `make test` command
add_test(NAME ${PROJECT_TEST_EXECUTABLE} COMMAND ${PROJECT_TEST_EXECUTABLE})
