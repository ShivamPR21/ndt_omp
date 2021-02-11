cmake_minimum_required(VERSION 3.5)
project(ndt_omp)

list(APPEND CMAKE_MODULE_PATH ${CMAKE_CURRENT_LIST_DIR}/cmake_modules)

# should use march=native ?
add_definitions(-std=c++14 -msse -msse2 -msse3 -msse4 -msse4.1 -msse4.2)
set(CMAKE_CXX_FLAGS "-std=c++14 -msse -msse2 -msse3 -msse4 -msse4.1 -msse4.2")

set(CMAKE_BUILD_TYPE "DEBUG")

find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(rclcpp_lifecycle REQUIRED)
find_package(yaml_cpp_vendor REQUIRED)
find_package(std_msgs REQUIRED)
find_package(tf2 REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(pcl_conversions REQUIRED)
find_package(Eigen3 3.3 REQUIRED NO_MODULE)
find_package(PCL 1.10 REQUIRED common io)

include_directories(${PCL_INCLUDE_DIRS})
link_directories(${PCL_LIBRARY_DIRS})
add_definitions(${PCL_DEFINITIONS})

message(STATUS "PCL_INCLUDE_DIRS:" ${PCL_INCLUDE_DIRS})
message(STATUS "PCL_LIBRARY_DIRS:" ${PCL_LIBRARY_DIRS})
message(STATUS "PCL_DEFINITIONS:" ${PCL_DEFINITIONS})

find_package(OpenMP)
if (OPENMP_FOUND)
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
endif ()

####################################
### catkin specific configuration ##
####################################
#catkin_package(
#  INCLUDE_DIRS include
#  LIBRARIES ndt_omp
#)
#
############
### Build ##
############
include_directories(include)
#
add_library(ndt_omp
  src/pclomp/voxel_grid_covariance_omp.cpp
  src/pclomp/ndt_omp.cpp
  src/pclomp/gicp_omp.cpp
)
#
#add_executable(align
#  apps/align.cpp
#)

#target_link_libraries(align
#  ${PCL_LIBRARIES}
#  ndt_omp
#)

#
#############
### INSTAL ##
#############
#
#install(
#  TARGETS
#    ndt_omp
#  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
#  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
#  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
#)
#
## install headers
#install(DIRECTORY include/pclomp
#        DESTINATION ${CATKIN_GLOBAL_INCLUDE_DESTINATION})

ament_package()