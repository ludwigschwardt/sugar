# Copyright (c) 2013, Ruslan Baratov
# All rights reserved.

# Link universal library

cmake_minimum_required(VERSION 2.8.8)
project(06-example-ios.link_library CXX)

include($ENV{SUGAR_ROOT}/cmake/Sugar)

include(sugar_include)
include(sugar_mark_macosx_resources)
include(sugar_set_xcode_ios_sdkroot)

sugar_include("./sources")
sugar_include("${SUGAR_ROOT}/examples/resources/ios/images")

if(NOT XCODE_VERSION)
  sugar_fatal_error("Xcode only")
endif()

set(PLIST_FILE "${SUGAR_ROOT}/examples/plist/ios/empty.plist.in")
set(MACOSX_BUNDLE_GUI_IDENTIFIER "com.example.sugar.ios.LinkLibrary")

sugar_mark_macosx_resources(${DEFAULT_IOS_IMAGES})

# searching for library
set(
    universal_path
    "${SUGAR_ROOT}/examples/06-ios/_universal_library/install"
)

# where *.hpp files will be searched
include_directories("${universal_path}/include")

# where *.a files will be searched
list(APPEND CMAKE_LIBRARY_PATH "${universal_path}/lib/ios")

# find debug and release versions of universal library
find_library(universal_release NAMES universal_lib_example)
find_library(universal_debug NAMES universal_lib_exampled)
if(NOT universal_release OR NOT universal_debug)
  sugar_fatal_error("Universal library not found (install it!)")
endif()

# set variable to describe both library versions
set(universal debug ${universal_debug} optimized ${universal_release})

# creating executable
add_executable(example MACOSX_BUNDLE ${SOURCES} ${DEFAULT_IOS_IMAGES})
sugar_set_xcode_ios_sdkroot(example ${PLIST_FILE})
target_link_libraries(example ${universal})
