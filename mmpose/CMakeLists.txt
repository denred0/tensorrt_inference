cmake_minimum_required(VERSION 3.5)

project(mmpose_trt)

set(CMAKE_CXX_STANDARD 14)

# CUDA
SET(CMAKE_CUDA_COMPILER /usr/local/cuda-11.1/bin/nvcc)
SET(CUDA_TOOLKIT_ROOT_DIR /usr/local/cuda-11.1)
SET(TENSORRT_INCLUDE_DIR "/home/vid/tensorrt/TensorRT-7.2.3.4/include")
SET(TENSORRT_LIBRARY_INFER "/home/vid/tensorrt/TensorRT-7.2.3.4/lib/libnvinfer.so")
SET(TENSORRT_LIBRARY_ONNXPARSER "/home/vid/tensorrt/TensorRT-7.2.3.4/lib/libnvonnxparser.so")


find_package(CUDA REQUIRED)
message(STATUS "Find CUDA include at ${CUDA_INCLUDE_DIRS}")
message(STATUS "Find CUDA libraries: ${CUDA_LIBRARIES}")

# TensorRT
set(TENSORRT_ROOT /usr/src/tensorrt/)
find_path(TENSORRT_INCLUDE_DIR NvInfer.h
        HINTS ${TENSORRT_ROOT} PATH_SUFFIXES include/)
message(STATUS "Found TensorRT headers at ${TENSORRT_INCLUDE_DIR}")
find_library(TENSORRT_LIBRARY_INFER nvinfer
        HINTS ${TENSORRT_ROOT} ${TENSORRT_BUILD} ${CUDA_TOOLKIT_ROOT_DIR}
        PATH_SUFFIXES lib lib64 lib/x64)
find_library(TENSORRT_LIBRARY_ONNXPARSER nvonnxparser
        HINTS  ${TENSORRT_ROOT} ${TENSORRT_BUILD} ${CUDA_TOOLKIT_ROOT_DIR}
        PATH_SUFFIXES lib lib64 lib/x64)
set(TENSORRT_LIBRARY ${TENSORRT_LIBRARY_INFER} ${TENSORRT_LIBRARY_ONNXPARSER})
message(STATUS "Find TensorRT libs: ${TENSORRT_LIBRARY}")

# OpenCV
find_package(OpenCV REQUIRED)
message(STATUS "Find OpenCV include at ${OpenCV_INCLUDE_DIRS}")
message(STATUS "Find OpenCV libraries: ${OpenCV_LIBRARIES}")

set(COMMON_INCLUDE ../includes/common)
set(YAML_INCLUDE ../includes/yaml-cpp/include)
set(YAML_LIB_DIR ../includes/yaml-cpp/libs)

include_directories(${CUDA_INCLUDE_DIRS} ${TENSORRT_INCLUDE_DIR} ${OpenCV_INCLUDE_DIRS} ${COMMON_INCLUDE} ${YAML_INCLUDE})
link_directories(${YAML_LIB_DIR})

# SET(NVPARSERS /home/vid/tensorrt/TensorRT-7.2.3.4/targets/x86_64-linux-gnu/lib/libnvparsers.so)

add_executable(mmpose_trt main.cpp mmpose.cpp ../includes/common/model.cpp ../includes/common/common.cpp)
target_link_libraries(mmpose_trt ${OpenCV_LIBRARIES} ${CUDA_LIBRARIES} ${TENSORRT_LIBRARY} yaml-cpp)
