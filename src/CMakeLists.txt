#
# project's executable configuration
#

#
# Search recursively within this folder searching for headers
#

file(GLOB_RECURSE PROJECT_HEADERS *.hpp *.h)


#
# Sources: list your .cpp sources here
#

set(PROJECT_SOURCES
    # sources: add .cpp files here below...
    dummy.cpp
)


#
# Common objects target
#
# We will share object files produced from these sources with test project(s),
# avoiding multiple compilations of the same file. The produced library will be
# more _a list of object files_ rather than an actual library.
#

add_library(
    ${COMMON_OBJS_TARGET}
    OBJECT
    # headers
    ${PROJECT_HEADERS}
    # sources
    ${PROJECT_SOURCES}
)

target_include_directories(
    ${COMMON_OBJS_TARGET}
    PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}
)


#
# Main project executable target
#

set(TARGET_EXECUTABLE ${PROJECT_NAME})

add_executable(
    ${TARGET_EXECUTABLE}
    # main
    main.cpp
)

target_include_directories(
    ${TARGET_EXECUTABLE}
    PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}
)


target_link_libraries(
    ${TARGET_EXECUTABLE}
    PRIVATE ${CMAKE_THREAD_LIBS_INIT}
    PRIVATE ${ZLIB_LIBRARIES}
)
