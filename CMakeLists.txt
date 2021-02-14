cmake_minimum_required(VERSION 2.8)

project (waifu2x-caffe)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++14 -fpermissive -Wunused-variable ")
set(CMAKE_C_FLAGS "${CMAKE_CXX_FLAGS} -Wunused-variable ")
set(CMAKE_CPP_FLAGS "${CMAKE_CXX_FLAGS} -std=c++14 -fpermissive -Wunused-variable ")

# Caffe
add_subdirectory(caffe libcaffe)
set(Caffe_INCLUDE_DIR ${CMAKE_SOURCE_DIR}/caffe/include)
set(Caffe_DIR ${CMAKE_CURRENT_BINARY_DIR}/libcaffe)
set(CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/cmake)

# CUDA
set(USE_CUDNN ON)
include(cmake/FindcuDNN.cmake)
detect_cuDNN()

# OpenCV
set(OpenCV_DIR /usr/lib/opencv-install/)
find_package(OpenCV REQUIRED core imgproc highgui)

file(GLOB common_srcs common/*.cpp)
set(exec_src ${CMAKE_SOURCE_DIR}/waifu2x-caffe/Source.cpp)

link_directories(${Caffe_DIR}/lib)
find_package( Boost COMPONENTS filesystem system iostreams REQUIRED )
find_package( Glog COMPONENTS REQUIRED)
find_package( Protobuf REQUIRED )
find_package( CUDA REQUIRED )
include_directories(${CMAKE_SOURCE_DIR}/rapidjson/include ${CMAKE_SOURCE_DIR}/include ${CMAKE_SOURCE_DIR}/common ${CMAKE_SOURCE_DIR}/stb ${CMAKE_SOURCE_DIR}/msgpack-c/include)
include_directories( ${Boost_INCLUDE_DIRS} )
include_directories( ${CUDA_INCLUDE_DIRS} )
include_directories( ${OpenCV_INCLUDE_DIRS} )
include_directories( ${Caffe_INCLUDE_DIR} )
include_directories( ${Caffe_DIR}/include )
include_directories( ${Glog_INCLUDE_DIRS} )
include_directories( ${CUDNN_INCLUDE} )
include_directories( /usr/include/hdf5/serial/ )
link_directories( /usr/lib/hdf5/serial/ )

add_executable(waifu2x-caffe ${exec_src} ${common_srcs})

target_link_libraries(waifu2x-caffe caffe)
target_link_libraries(waifu2x-caffe ${Boost_LIBRARIES} ${OpenCV_LIBS} ${GLOG_LIBRARIES} ${PROTOBUF_LIBRARIES} ${CUDA_LIBRARIES})

CUDA_ADD_CUBLAS_TO_TARGET(waifu2x-caffe)
