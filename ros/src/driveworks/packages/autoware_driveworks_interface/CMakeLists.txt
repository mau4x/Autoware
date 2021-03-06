cmake_minimum_required(VERSION 2.8.3)
project(autoware_driveworks_interface)

EXECUTE_PROCESS(
  COMMAND uname -m
  OUTPUT_VARIABLE ARCH
  OUTPUT_STRIP_TRAILING_WHITESPACE
)

if("${ARCH}" STREQUAL "aarch64")


include(FindPkgConfig)
find_package(CUDA REQUIRED)
find_package(Threads REQUIRED)
find_package(OpenCV REQUIRED)

find_package(catkin REQUIRED COMPONENTS
  roscpp
  cv_bridge
  std_msgs
  sensor_msgs
  autoware_msgs
  tf
)


catkin_package(
  CATKIN_DEPENDS std_msgs sensor_msgs autoware_msgs
)

set(CMAKE_CXX_FLAGS "-O2 -s  -std=c++11 -Wno-error=unused-parameter")

include_directories(
	include
	${catkin_INCLUDE_DIRS}
	${Boost_INCLUDE_DIRS}
	${OpenCV_INCLUDE_DIRS}
)

link_directories(
	${catkin_LIBRARY_DIRS}
	${PCL_LIBRARY_DIRS}
	${OpenCV_LIBRARY_DIRS}
	/usr/lib/
	lib
	/usr/local/driveworks/lib/
)

add_executable(gmsl_interface
 	nodes/gmsl_interface/gmsl_interface_node.cpp
)

add_dependencies(gmsl_interface
	${catkin_EXPORTED_TARGETS}
)

target_link_libraries(gmsl_interface
	${catkin_LIBRARIES}
	-lautoware_driveworks
)

add_executable(tensorrt_interface
	nodes/tensorrt_interface/tensorrt_interface_node.cpp
)

add_dependencies(tensorrt_interface
	${catkin_EXPORTED_TARGETS}
)

target_link_libraries(tensorrt_interface 
		${catkin_LIBRARIES}
	-lautoware_driveworks
)

endif()
