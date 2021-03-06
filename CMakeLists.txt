cmake_minimum_required(VERSION 3.0)

project(cis565_stream_compaction_test)

set(CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake" ${CMAKE_MODULE_PATH})

# Enable C++11 for host code
set(CMAKE_CXX_STANDARD 11)

list(APPEND CUDA_NVCC_FLAGS_DEBUG -G -g)
list(APPEND CUDA_NVCC_FLAGS_RELWITHDEBUGINFO -lineinfo)
list(APPEND CUDA_NVCC_FLAGS "-std=c++11")

# Crucial magic for CUDA linking
find_package(Threads REQUIRED)
find_package(CUDA REQUIRED)

set(CUDA_ATTACH_VS_BUILD_RULE_TO_CUDA_FILE ON)
set(CUDA_SEPARABLE_COMPILATION ON)

if(${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
    set(CUDA_PROPAGATE_HOST_FLAGS OFF)
endif()

include_directories(.)
add_subdirectory(stream_compaction)

cuda_add_executable(${CMAKE_PROJECT_NAME}
    "src/main.cpp"
    )

target_link_libraries(${CMAKE_PROJECT_NAME}
    stream_compaction
    ${CORELIBS}
    )
