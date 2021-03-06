# Copyright 2017-2018 by Martin Moene
#
# https://github.com/martinmoene/value_ptr-lite
#
# Distributed under the Boost Software License, Version 1.0.
# (See accompanying file LICENSE.txt or copy at http://www.boost.org/LICENSE_1_0.txt)

cmake_minimum_required( VERSION 3.5 FATAL_ERROR )

# value_ptr-lite project and version, updated by script/update-version.py:

project(
    value_ptr_lite
    VERSION 0.1.0
#   DESCRIPTION "A C++ smart-pointer with value semantics for C++98, C++11 and later in a single-file header-only library"
#   HOMEPAGE_URL "https://github.com/martinmoene/value_ptr-lite"
    LANGUAGES CXX )

# Package information:

set( unit_name       "value_ptr" )
set( package_nspace  "nonstd" )
set( package_name    "${unit_name}-lite" )
set( package_version "${${PROJECT_NAME}_VERSION}" )

message( STATUS "Project '${PROJECT_NAME}', package '${package_name}' version: '${package_version}'")

# Toplevel or subproject:

if ( CMAKE_PROJECT_NAME STREQUAL PROJECT_NAME )
    set( value_ptr_IS_TOPLEVEL_PROJECT TRUE )
else()
    set( value_ptr_IS_TOPLEVEL_PROJECT FALSE )
endif()

# If toplevel project, enable building and performing of tests, disable building of examples:

option( VALUE_PTR_LITE_BUILD_TEST    "Build and perform value_ptr-lite tests" ${value_ptr_IS_TOPLEVEL_PROJECT} )
option( VALUE_PTR_LITE_BUILD_EXAMPLE "Build value_ptr-lite examples" OFF )

# If requested, build and perform tests, build examples:

if ( VALUE_PTR_LITE_BUILD_TEST )
    enable_testing()
    add_subdirectory( test )
endif()

if ( VALUE_PTR_LITE_BUILD_EXAMPLE )
    enable_testing()
    add_subdirectory( example )
endif()

#
# Interface, installation and packaging
#

# CMake helpers:

include( GNUInstallDirs )
include( CMakePackageConfigHelpers )

# Interface library:

add_library(
    ${package_name} INTERFACE )

add_library(
    ${package_nspace}::${package_name} ALIAS ${package_name} )

target_include_directories(
    ${package_name}
    INTERFACE
        "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
        "$<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>" )

# Package configuration:
# Note: package_name and package_target are used in package_config_in

set( package_folder         "${package_nspace}" )
set( package_target         "${package_name}-targets" )
set( package_config         "${package_name}-config.cmake" )
set( package_config_in      "${package_name}-config.cmake.in" )
set( package_config_version "${package_name}-config-version.cmake" )

configure_package_config_file(
    "${CMAKE_CURRENT_SOURCE_DIR}/cmake/${package_config_in}"
    "${CMAKE_CURRENT_BINARY_DIR}/${package_config}"
    INSTALL_DESTINATION "${CMAKE_INSTALL_LIBDIR}/cmake/${package_folder}"
)

write_basic_package_version_file(
    "${CMAKE_CURRENT_BINARY_DIR}/${package_config_version}"
    VERSION       ${package_version}
    COMPATIBILITY SameMajorVersion
)

# Installation:

install(
    TARGETS      ${package_name}
    EXPORT       ${package_target}
#   INCLUDES DESTINATION "${...}"  # already set via target_include_directories()
)

install(
    EXPORT       ${package_target}
    NAMESPACE    ${package_nspace}::
    DESTINATION "${CMAKE_INSTALL_LIBDIR}/cmake/${package_folder}"
)

install(
    FILES       "${CMAKE_CURRENT_BINARY_DIR}/${package_config}"
                "${CMAKE_CURRENT_BINARY_DIR}/${package_config_version}"
    DESTINATION "${CMAKE_INSTALL_LIBDIR}/cmake/${package_folder}"
)

install(
    DIRECTORY   "include/"
    DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}"
)

# end of file
