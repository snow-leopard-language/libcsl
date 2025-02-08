if (NOT DEFINED LLVM_MAIN_SRC_DIR)
    cmake_minimum_required(VERSION 3.30.0)
    set(CMAKE_C_STANDARD 23)

    project(libcsl LANGUAGES C)

    set(LIBCLS_STANDALONE_BUILD TRUE)
endif()
set(LLVM_SUBPROJECT_TITLE "libcsl")

if (LIBCLS_STANDALONE_BUILD)
    find_package(LLVM REQUIRED CONFIG)
    if (LLVM_BUILD_MAIN_SRC_DIR)
        set(default_llvm_src "${LLVM_BUILD_MAIN_SRC_DIR}")
    else()
        set(default_llvm_src "${CMAKE_CURRENT_SOURCE_DIR}/../llvm")
    endif()
    set(LLVM_SOURCE_ROOT ${default_llvm_src} CACHE PATH "Path to LLVM source tree")

    # Enable unit tests if available.
    set(LIBCSL_GTEST_AVAIL 0)
    if(TARGET llvm_gtest)
        set(LIBCSL_GTEST_AVAIL 1)
    endif()

    set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/bin")
else ()
    set(LLVM_SOURCE_ROOT "${LLVM_MAIN_SRC_DIR}")
    set(LIBCSL_GTEST_AVAIL 1)
endif ()

set(LIBCSL_SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR})
set(LIBCSL_BINARY_DIR ${CMAKE_CURRENT_BINARY_DIR})

if(NOT DEFINED LLVM_COMMON_CMAKE_UTILS)
    set(LLVM_COMMON_CMAKE_UTILS ${LIBCSL_SOURCE_DIR}/../cmake)
endif()




include_directories(${LLVM_INCLUDE_DIRS})
separate_arguments(LLVM_DEFINITIONS_LIST NATIVE_COMMAND ${LLVM_DEFINITIONS})
add_definitions(${LLVM_DEFINITIONS_LIST})

list(APPEND CMAKE_MODULE_PATH "${LLVM_CMAKE_DIR}")
include(AddLLVM)
llvm_map_components_to_libnames(llvm_libs support core irreader)

add_library(LLVMPassname MODULE main.c)
add_executable(libcsl main.c)
