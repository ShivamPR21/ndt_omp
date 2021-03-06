cmake_minimum_required(VERSION 3.5)
project(ndt_omp)

list(APPEND CMAKE_MODULE_PATH ${CMAKE_CURRENT_LIST_DIR}/cmake_modules)

# should use march=native ?
# add_definitions(-std=c++14 -msse -msse2 -msse3 -msse4 -msse4.1 -msse4.2)
# set(CMAKE_CXX_FLAGS "-std=c++14 -msse -msse2 -msse3 -msse4 -msse4.1 -msse4.2")
# Set target C++ standard and required compiler features
set(CMAKE_CXX_STANDARD 14 CACHE STRING "The target C++ standard. PCL requires C++14 or higher.")
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# check for SSE flags
include("${CMAKE_CURRENT_LIST_DIR}/cmake/find_sse.cmake")
# if(PCL_ENABLE_SSE AND "${CMAKE_CXX_FLAGS}" STREQUAL "${CMAKE_CXX_FLAGS_DEFAULT}")
  PCL_CHECK_FOR_SSE()
# endif()

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${SSE_FLAGS_STR}")

# set(CMAKE_BUILD_TYPE "DEBUG")

find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
# find_package(rclcpp_lifecycle REQUIRED)
# find_package(yaml_cpp_vendor REQUIRED)
# find_package(std_msgs REQUIRED)
# find_package(tf2 REQUIRED)
# find_package(sensor_msgs REQUIRED)
# find_package(geometry_msgs REQUIRED)
# find_package(pcl_conversions REQUIRED)
# find_package(Eigen3 3.3 REQUIRED NO_MODULE)
find_package(PCL 1.11 REQUIRED common io)
find_package(OpenMP)

include_directories(include)


add_library(ndt_omp
  src/pclomp/voxel_grid_covariance_omp.cpp
  src/pclomp/ndt_omp.cpp
  src/pclomp/gicp_omp.cpp
)

target_link_libraries(ndt_omp PUBLIC ${OpenMP_CXX_FLAGS} ${PCL_LIBRARIES})
target_compile_options(ndt_omp PUBLIC ${OpenMP_CXX_FLAGS})
#
add_executable(align
 apps/align.cpp
)

target_link_libraries(align
 ${PCL_LIBRARIES}
 ndt_omp
)

#
#############
### INSTAL ##
#############
#
install(TARGETS ndt_omp align
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin
)
#
# install headers
install(DIRECTORY include/
       DESTINATION include/)

ament_package()
