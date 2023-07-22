cmake_minimum_required(VERSION 3.22)
project(xz C ASM)
include(FetchContent)

if (NOT CMAKE_BUILD_TYPE STREQUAL "Debug")
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Ofast -funroll-loops -flto=thin \
           -mllvm -polly \
           -mllvm -polly-run-dce \
           -mllvm -polly-run-inliner \
           -mllvm -polly-isl-arg=--no-schedule-serialize-sccs \
           -mllvm -polly-ast-use-context \
           -mllvm -polly-detect-keep-going \
           -mllvm -polly-position=before-vectorizer \
           -mllvm -polly-vectorizer=stripmine \
           -mllvm -polly-detect-profitability-min-per-loop-insts=40 \
           -mllvm -polly-invariant-load-hoisting")
endif (NOT CMAKE_BUILD_TYPE STREQUAL "Debug")

# Change the default path of fetch content to avoid downloading the same dependencies per architecture
get_filename_component(deps "../_deps" REALPATH BASE_DIR "${CMAKE_BINARY_DIR}")
set(FETCHCONTENT_BASE_DIR ${deps})

FetchContent_Declare(liblzma
        GIT_REPOSITORY https://git.tukaani.org/xz.git
        GIT_TAG v5.4.3
        PATCH_COMMAND git apply ${CMAKE_CURRENT_LIST_DIR}/0001-Don-t-build-other-binarys.patch || cd .
)

FetchContent_MakeAvailable(liblzma)
