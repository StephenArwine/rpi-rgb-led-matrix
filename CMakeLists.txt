cmake_minimum_required(VERSION 3.16.3)

project(rgbmatrix)

if (NOT EXISTS ${CMAKE_BINARY_DIR}/CMakeCache.txt)
    if (NOT CMAKE_BUILD_TYPE)
        set(CMAKE_BUILD_TYPE "Release" CACHE STRING "" FORCE)
        message(STATUS "No previous build - default to Release build")
    endif()
endif()

set (CMAKE_EXPORT_COMPILE_COMMANDS ON)
set (CMAKE_CXX_STANDARD 17)
add_compile_options(-Wall -Wextra -pedantic -Wno-unused-parameter -Werror -Wfatal-errors)
add_definitions(-D_FILE_OFFSET_BITS=64)

if (CMAKE_COMPILER_IS_GNUCXX)
    add_compile_options(-Wno-psabi)
endif()

option(BUILD_SHARED_LIBS "Build using shared libraries" ON)

# Source package generation setup.
set(CPACK_GENERATOR "TXZ")
set(CPACK_PACKAGE_FILE_NAME "rgbmatrix-build")
set(CPACK_SOURCE_GENERATOR "TXZ")
set(CPACK_INSTALL_SCRIPTS ${CMAKE_SOURCE_DIR}/package.cmake)
set(CPACK_SOURCE_PACKAGE_FILE_NAME "rgbmatrix-src")
set(CPACK_SOURCE_IGNORE_FILES "/\.git*;/build;")
include(CPack)

include(GNUInstallDirs)


file(GLOB_RECURSE SRC_FILES lib/.cc lib/.c)
file(GLOB_RECURSE HEADER_FILES include/*.h)

set(SOURCES
    lib/bdf-font.cc
    lib/content-streamer.cc
    lib/framebuffer.cc
    lib/gpio.cc
    lib/graphics.cc
    lib/led-matrix.cc
    lib/led-matrix-c.cc
    lib/multiplex-mappers.cc
    lib/options-initialize.cc
    lib/pixel-mapper.cc
    lib/thread.cc
    lib/hardware-mapping.c
)

set(HEADERS
    include/canvas.h
    include/content-streamer.h
    include/graphics.h
    include/led-matrix.h
    include/led-matrix-c.h
    include/pixel-mapper.h
    include/thread.h
    include/threaded-canvas-manipulator.h
    lib/hardware-mapping.h
)

add_library(librgbmatrix ${SOURCES} ${HEADERS})
target_include_directories(librgbmatrix
        PUBLIC
        include
)


set_target_properties(librgbmatrix PROPERTIES PREFIX "" IMPORT_PREFIX "")
target_link_libraries(librgbmatrix pthread )

install(TARGETS librgbmatrix 
    LIBRARY DESTINATION "/usr/lib" 
    ARCHIVE DESTINATION "/usr/include"
)

install(
    FILES ${HEADERS} DESTINATION "/usr/include"
)