cmake_minimum_required(VERSION 2.8.12)
project(ORB_SLAM2)

SET(CMAKE_EXPORT_COMPILE_COMMANDS "ON")

IF(NOT CMAKE_BUILD_TYPE)
  SET(CMAKE_BUILD_TYPE Release)
ENDIF()

MESSAGE("Build type: " ${CMAKE_BUILD_TYPE})

set(CMAKE_CXX_FLAGS "-std=c++11 ${CMAKE_CXX_FLAGS}")
set(CMAKE_C_FLAGS "-Wall -Wno-unused-result -Wno-unknown-pragmas -Wno-unused-variable -Wfatal-errors -fPIC ${CMAKE_C_FLAGS}")

# set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS}  -Wall  -O3 -march=native ")
# set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall   -O3 -march=native")
# set(CXX_COMPILER_FLAGS -03 -ffast-math -fomit-frame-pointer -funroll-loops -fopenmp=libomp -msse2)

# Check C++11 or C++0x support
#include(CheckCXXCompilerFlag)
#CHECK_CXX_COMPILER_FLAG("-std=c++11" COMPILER_SUPPORTS_CXX11)
#CHECK_CXX_COMPILER_FLAG("-std=c++0x" COMPILER_SUPPORTS_CXX0X)
#if(COMPILER_SUPPORTS_CXX11)
#   set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
#   add_definitions(-DCOMPILEDWITHC11)
#   message(STATUS "Using flag -std=c++11.")
#elseif(COMPILER_SUPPORTS_CXX0X)
#   set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++0x")
#   add_definitions(-DCOMPILEDWITHC0X)
#   message(STATUS "Using flag -std=c++0x.")
#else()
#   message(FATAL_ERROR "The compiler ${CMAKE_CXX_COMPILER} has no C++11 support. Please use a different C++ compiler.")
#endif()

# SET(CMAKE_C_COMPILER "/usr/bin/gcc-5")

# SET(CMAKE_CXX_COMPILER "/usr/bin/g++-5")
# set(CMAKE_C_STANDARD 11)
# add_definitions(-std=c++11)
#list(APPEND CMAKE_C_FLAGS "-std=c++11 -O3 -ffast-math -Wno-unused-result")
# include(FindCUDA)
# LIST(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake_modules)
# LIST(APPEND CUDA_NVCC_FLAGS --compiler-options -fno-strict-aliasing -lineinfo -use_fast_math -Xptxas -dlcm=cg)
# LIST(APPEND CUDA_NVCC_FLAGS -gencode arch=compute_30,code=sm_30)   
# LIST(APPEND CUDA_NVCC_FLAGS -gencode arch=compute_35,code=sm_35)    #for gtx9XX
# LIST(APPEND CUDA_NVCC_FLAGS -gencode arch=compute_50,code=sm_50)    #for maxwell
# LIST(APPEND CUDA_NVCC_FLAGS -gencode arch=compute_52,code=sm_52)    #for maxwell
# LIST(APPEND CUDA_NVCC_FLAGS -gencode arch=compute_60,code=sm_60)    #for pascal
# LIST(APPEND CUDA_NVCC_FLAGS -gencode arch=compute_61,code=sm_61)    #for pascal
# LIST(APPEND CUDA_NVCC_FLAGS -gencode arch=compute_62,code=sm_62) #for jetson tx2

#find_package(OpenCV 3.0 QUIET)
#if(NOT OpenCV_FOUND)
#find_package(OpenCV 2.4.3 QUIET)
#   if(NOT OpenCV_FOUND)
#      message(FATAL_ERROR "OpenCV > 2.4.3 not found.")
#   endif()
#endif()

############################################################
############################################################
########### IMPORTANT ######################################
#### openni lib, make sure to set it with your own path#####
#set(OPENNI_INCLUDE_DIR "${PROJECT_SOURCE_DIR}/Thirdparty/OpenNI-Linux-x64-2.3/Include")
#set(OPENNI_LIB_DIR "${PROJECT_SOURCE_DIR}/Thirdparty/OpenNI-Linux-x64-2.3/Redist")
############################################################
############################################################
############################################################
############################################################
############################################################

find_path(DARKNET_PATH
  NAMES "README.md"
  HINTS "/home/yubao/data/catkin_ws/src/darknet_ros/darknet")
message(STATUS "Darknet path dir = ${DARKNET_PATH}")
add_definitions(-DDARKNET_FILE_PATH="${DARKNET_PATH}")

# Find CUDA
find_package(CUDA QUIET)
if (CUDA_FOUND)
  find_package(CUDA REQUIRED)
  message(STATUS "CUDA Version: ${CUDA_VERSION_STRINGS}")
  message(STATUS "CUDA Libararies: ${CUDA_LIBRARIES}")
  set(
    CUDA_NVCC_FLAGS
    ${CUDA_NVCC_FLAGS};
    -O3
    -gencode arch=compute_30,code=sm_30
    -gencode arch=compute_35,code=sm_35
    -gencode arch=compute_50,code=[sm_50,compute_50]
    -gencode arch=compute_52,code=[sm_52,compute_52]
    -gencode arch=compute_61,code=sm_61
    -gencode arch=compute_62,code=sm_62
  )
  add_definitions(-DGPU)
else()
  list(APPEND LIBRARIES "m")
endif()


  link_directories(
    /usr/local/cuda/lib64
  )


# Find X11
message ( STATUS "Searching for X11..." )
find_package ( X11 REQUIRED )
if ( X11_FOUND )
  include_directories ( ${X11_INCLUDE_DIR} )
  link_libraries ( ${X11_LIBRARIES} )
  message ( STATUS " X11_INCLUDE_DIR: " ${X11_INCLUDE_DIR} )
  message ( STATUS " X11_LIBRARIES: " ${X11_LIBRARIES} )
endif ( X11_FOUND )


find_package(Eigen3  REQUIRED)
find_package(Pangolin REQUIRED)
# adding for point cloud viewer and mapper
find_package(PCL 1.8 REQUIRED HINTS "~/data/software/pcl-1.8")
ADD_DEFINITIONS( ${PCL_DEFINITIONS} )

find_package(OpenCV 3.3  REQUIRED)
# find_package(CUDA  REQUIRED)
find_package(OpenMP)
find_package(Boost REQUIRED COMPONENTS thread)


#set(PCL_INCLUDE_DIRS /usr/local/include/pcl-1.8)



add_definitions(-DGPU)
add_definitions(-DOPENCV)
add_definitions(-DCUDNN)
add_definitions(-D_TIMESPEC_DEFINED)
add_definitions(-D_TIMESPEC_DEFINED)
#set(DARKNET_PATH ${PROJECT_SOURCE_DIR}/Thirdparty/darknet)
# set(DARKNET_PATH /home/yubao/data/catkin_ws/src/darknet_ros/darknet)
# set(DARKNET_LIB /home/yubao/data/catkin_ws/src/darknet_ros/darknet/libdarknet.so)
# add_definitions(-DDARKNET_FILE_PATH="${DARKNET_PATH}")

include_directories(
  ${PROJECT_SOURCE_DIR}
  ${PROJECT_SOURCE_DIR}/include
  ${OpenCV_INCLUDE_DIRS}
  # openni
  # ${OPENNI_INCLUDE_DIR}
  #${OPENNI2_INCLUDE_DIR}
  ${EIGEN3_INCLUDE_DIR}
  ${Pangolin_INCLUDE_DIRS}
  ${PCL_INCLUDE_DIRS}
  ${CUDA_INCLUDE_DIRS}
  ${DARKNET_PATH}/src
  ${DARKNET_PATH}/include
  ${CMAKE_CURRENT_SOURCE_DIR}/gco-v3.0
  /usr/local/cuda/include
)


add_definitions(${PCL_DEFINITIONS} )

set(CUDA_INCLUDE_DIR /usr/local/cuda/include)
#set(CUDNN_LIBRARY_DIR /usr/local/cuda-9.0/targets/x86_64-linux/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/lib)

set(HEADERS
    include/gco-v3.0/graph.h
    include/gco-v3.0/block.h
    include/gco-v3.0/energy.h
    include/gco-v3.0/GCoptimization.h
    include/gco-v3.0/LinkedBlockList.h
    include/config.h
    include/lsa_tr.h
    include/segmentation.h
    include/segmentation_helpers.h
    )

add_library(${PROJECT_NAME} SHARED
src/System.cc
src/Tracking.cc
src/LocalMapping.cc
src/LoopClosing.cc
src/ORBextractor.cc
src/ORBmatcher.cc
src/FrameDrawer.cc
src/Converter.cc
src/MapPoint.cc
src/KeyFrame.cc
src/Map.cc
src/MapDrawer.cc
src/Optimizer.cc
src/PnPsolver.cc
src/Frame.cc
src/KeyFrameDatabase.cc
src/Sim3Solver.cc
src/Initializer.cc
src/Viewer.cc
src/pointcloudmapping.cc
src/segmentation.cc
gco-v3.0/maxflow.cpp gco-v3.0/maxflow.cpp gco-v3.0/LinkedBlockList.cpp gco-v3.0/GCoptimization.cpp gco-v3.0/graph.cpp ${HEADERS}

${DARKNET_PATH}/src/activation_layer.c        ${DARKNET_PATH}/src/im2col.c
${DARKNET_PATH}/src/activations.c             ${DARKNET_PATH}/src/image.c
${DARKNET_PATH}/src/avgpool_layer.c           ${DARKNET_PATH}/src/layer.c
${DARKNET_PATH}/src/batchnorm_layer.c         ${DARKNET_PATH}/src/list.c
${DARKNET_PATH}/src/blas.c                    ${DARKNET_PATH}/src/local_layer.c
${DARKNET_PATH}/src/box.c                     ${DARKNET_PATH}/src/lstm_layer.c
${DARKNET_PATH}/src/col2im.c                  ${DARKNET_PATH}/src/matrix.c
${DARKNET_PATH}/src/connected_layer.c         ${DARKNET_PATH}/src/maxpool_layer.c
${DARKNET_PATH}/src/convolutional_layer.c     ${DARKNET_PATH}/src/network.c
${DARKNET_PATH}/src/cost_layer.c              ${DARKNET_PATH}/src/normalization_layer.c
${DARKNET_PATH}/src/crnn_layer.c              ${DARKNET_PATH}/src/option_list.c
${DARKNET_PATH}/src/crop_layer.c              ${DARKNET_PATH}/src/parser.c
${DARKNET_PATH}/src/cuda.c                    ${DARKNET_PATH}/src/region_layer.c
${DARKNET_PATH}/src/data.c                    ${DARKNET_PATH}/src/reorg_layer.c
${DARKNET_PATH}/src/deconvolutional_layer.c   ${DARKNET_PATH}/src/rnn_layer.c
${DARKNET_PATH}/src/demo.c                    ${DARKNET_PATH}/src/route_layer.c
${DARKNET_PATH}/src/detection_layer.c         ${DARKNET_PATH}/src/shortcut_layer.c
${DARKNET_PATH}/src/dropout_layer.c           ${DARKNET_PATH}/src/softmax_layer.c
${DARKNET_PATH}/src/gemm.c                    ${DARKNET_PATH}/src/tree.c
${DARKNET_PATH}/src/gru_layer.c               ${DARKNET_PATH}/src/utils.c
${DARKNET_PATH}/src/upsample_layer.c          ${DARKNET_PATH}/src/logistic_layer.c
${DARKNET_PATH}/src/l2norm_layer.c            ${DARKNET_PATH}/src/yolo_layer.c

${DARKNET_PATH}/examples/art.c                ${DARKNET_PATH}/examples/lsd.c
${DARKNET_PATH}/examples/attention.c          ${DARKNET_PATH}/examples/nightmare.c
${DARKNET_PATH}/examples/captcha.c            ${DARKNET_PATH}/examples/regressor.c
${DARKNET_PATH}/examples/cifar.c              ${DARKNET_PATH}/examples/rnn.c
${DARKNET_PATH}/examples/classifier.c         ${DARKNET_PATH}/examples/segmenter.c
${DARKNET_PATH}/examples/coco.c               ${DARKNET_PATH}/examples/super.c
${DARKNET_PATH}/examples/darknet.c            ${DARKNET_PATH}/examples/tag.c
${DARKNET_PATH}/examples/detector.c           ${DARKNET_PATH}/examples/yolo.c
${DARKNET_PATH}/examples/go.c

${DARKNET_PATH}/src/activation_kernels.cu     ${DARKNET_PATH}/src/crop_layer_kernels.cu
${DARKNET_PATH}/src/avgpool_layer_kernels.cu  ${DARKNET_PATH}/src/deconvolutional_kernels.cu
${DARKNET_PATH}/src/blas_kernels.cu           ${DARKNET_PATH}/src/dropout_layer_kernels.cu
${DARKNET_PATH}/src/col2im_kernels.cu         ${DARKNET_PATH}/src/im2col_kernels.cu
${DARKNET_PATH}/src/convolutional_kernels.cu  ${DARKNET_PATH}/src/maxpool_layer_kernels.cu
)

target_link_libraries(${PROJECT_NAME}
  ${OpenCV_LIBS}
  ${EIGEN3_LIBS}
  ${Pangolin_LIBRARIES}
  ${PROJECT_SOURCE_DIR}/Thirdparty/DBoW2/lib/libDBoW2.so
  ${PROJECT_SOURCE_DIR}/Thirdparty/g2o/lib/libg2o.so
  #${PROJECT_SOURCE_DIR}/Thirdparty/darknet/build/libYOLOv3SE.so
  #/home/yubao/data/catkin_ws/src/darknet_ros/darknet/libdarknet.so
  ${DARKNET_PATH}/libdarknet.so
  #/usr/local/cuda/lib64/libcudnn.so
  # /usr/lib/x86_64-linux-gnu/libcudnn.so
  ${PCL_LIBRARIES}
  #openni
  # libOpenNI2.so
  #${OPENNI_LIB_DIR}/libOpenNI2.so
  #${OPENNI2_LIB_DIR}/libOpenNI2.so
  m
  dl
  pthread
  stdc++
  cuda
  cudart
  cublas
  curand
)


#CONFIGURE_FILE(./src/segmentation.pc.in segmentation.pc @ONLY)
# Build examples

set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)
#add_executable(rgbd_astra
#Examples/RGB-D/rgbd_astra.cpp)
#target_link_libraries(rgbd_astra ${PROJECT_NAME})

#add_executable(rgbd_xtion_cc
#Examples/RGB-D/rgbd_xtion_cc.cpp)
#target_link_libraries(rgbd_xtion_cc ${PROJECT_NAME})

add_executable(rgbd_tum
    Examples/RGB-D/rgbd_tum.cc)
target_link_libraries(rgbd_tum ${PROJECT_NAME} )


#add_executable(rgbd_my
#    Examples/RGB-D/rgbd_my.cc)
#target_link_libraries(rgbd_my ${PROJECT_NAME})

#add_executable(stereo_kitti
#Examples/Stereo/stereo_kitti.cc)
#target_link_libraries(stereo_kitti ${PROJECT_NAME})

#add_executable(stereo_euroc
#Examples/Stereo/stereo_euroc.cc)
#target_link_libraries(stereo_euroc ${PROJECT_NAME})

#add_executable(mono_tum
#Examples/Monocular/mono_tum.cc)
#target_link_libraries(mono_tum ${PROJECT_NAME})

#add_executable(mono_kitti
#Examples/Monocular/mono_kitti.cc)
#target_link_libraries(mono_kitti ${PROJECT_NAME})

#add_executable(mono_euroc
#Examples/Monocular/mono_euroc.cc)
#target_link_libraries(mono_euroc ${PROJECT_NAME})


# Build tools
#set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/tools)
#add_executable(bin_vocabulary
#tools/bin_vocabulary.cc)
#target_link_libraries(bin_vocabulary ${PROJECT_NAME})
