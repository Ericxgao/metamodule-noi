cmake_minimum_required(VERSION 3.24)

if (DEFINED ENV{METAMODULE_SDK_DIR})
    set(METAMODULE_SDK_DIR "$ENV{METAMODULE_SDK_DIR}")
elseif("${METAMODULE_SDK_DIR}" STREQUAL "")
    set(METAMODULE_SDK_DIR "${CMAKE_CURRENT_LIST_DIR}/../metamodule-plugin-sdk")
endif()
include(${METAMODULE_SDK_DIR}/plugin.cmake)

project(NOI VERSION 2.0.7 DESCRIPTION "NOI" LANGUAGES C CXX)

# Set C++17 standard
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Define source directories
set(SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/NOI-VCVRACK)
set(SRC_DIR ${SOURCE_DIR}/src)

add_library(NOI STATIC)

target_compile_definitions(NOI PRIVATE 
    __V1x 
    __PLUGIN
)

# Add compile options for better control over symbols
target_compile_options(NOI PRIVATE
    -fdata-sections
    -ffunction-sections
    -fno-exceptions
    -fno-threadsafe-statics
    -fno-use-cxa-atexit
    $<$<CXX_COMPILER_ID:GNU>:-fvisibility=hidden>
    $<$<CXX_COMPILER_ID:GNU>:-fvisibility-inlines-hidden>
    $<$<COMPILE_LANGUAGE:CXX>:-include ${CMAKE_CURRENT_LIST_DIR}/src/mutex-stub.hh>
)

# Add linker flags for static linking
target_link_options(NOI PRIVATE
    -Wl,--gc-sections
    -static-libgcc
    -static-libstdc++
)

# Add source files
target_sources(NOI PRIVATE
    ${SRC_DIR}/plugin.cpp
    ${SRC_DIR}/Dicotyledon.cpp
    ${SRC_DIR}/Hellebore.cpp
    ${SRC_DIR}/Pruners.cpp
    ${SRC_DIR}/Sinensis.cpp
    ${SRC_DIR}/Sunflower.cpp
    ${SRC_DIR}/Philodendron.cpp
    ${SRC_DIR}/RingBuffer.cpp
    ${SRC_DIR}/Wilt.cpp
    ${SRC_DIR}/buffer.cpp
    ${SRC_DIR}/outils.cpp
    ${SRC_DIR}/reverb.cpp
    ${SRC_DIR}/filter.cpp
)

# Add include directories
target_include_directories(NOI PRIVATE 
    ${SOURCE_DIR}
    ${SRC_DIR}
)

# Create the plugin file
create_plugin(
    SOURCE_LIB      NOI
    PLUGIN_NAME     NOI
    PLUGIN_JSON     ${SOURCE_DIR}/plugin.json
    SOURCE_ASSETS   ${CMAKE_CURRENT_LIST_DIR}/assets
    DESTINATION     ${CMAKE_CURRENT_LIST_DIR}/metamodule-plugins
) 