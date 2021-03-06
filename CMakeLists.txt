cmake_minimum_required(VERSION 2.8.12)
project(frontal_delphi_radar)
set(CMAKE_INCLUDE_CURRENT_DIR ON)

set(PACKAGE_DEPENDENCIES
  eigen_conversions
  geometry_msgs
  nav_msgs
  rosbag
  roscpp
  roslib
  sensor_msgs
  std_msgs
  tf
  tf2
  tf2_eigen
  tf2_ros
  urdf
  visualization_msgs
  cv_bridge  
  image_transport
  sensor_driver_msgs
)

##############################
###### find packages #########
##############################
find_package(catkin REQUIRED COMPONENTS ${PACKAGE_DEPENDENCIES} message_generation)
set(BUILD_SHARED_LIBS OFF)




include(FindPkgConfig) #zhanghm: what mean?

find_package(PCL REQUIRED)
#set(OpenCV_DIR /usr/local/share/OpenCV)
find_package(OpenCV REQUIRED)
find_package(Eigen3 REQUIRED)
find_package(Boost REQUIRED COMPONENTS system iostreams filesystem regex program_options)

##############################
#### message generation ######
##############################
add_message_files(FILES RadarPoint.msg RadarData.msg)
generate_messages(DEPENDENCIES std_msgs)

catkin_package(
  CATKIN_DEPENDS ${PACKAGE_DEPENDENCIES} message_runtime
  DEPENDS EIGEN3 PCL OpenCV
  INCLUDE_DIRS src
)

#include headers
include_directories(SYSTEM  ${catkin_INCLUDE_DIRS}  ${PCL_INCLUDE_DIRS} ${Boost_INCLUDE_DIRS} ${EIGEN3_INCLUDE_DIR} src include)
include_directories(${OpenCV_INCLUDE_DIRS} ${catkin_INCLUDE_DIRS})

set(SRC_LIST src/TypeDef.h src/object_detection_radar.h src/frontal_delphi_radar.cpp src/main.cpp src/object_detection_radar.cpp)
##############################
#### add executable ##########
##############################

add_executable(frontal_delphi_radar ${SRC_LIST})
target_link_libraries(frontal_delphi_radar PUBLIC ${catkin_LIBRARIES}
										${PCL_LIBRARIES}  
										${Boost_LIBRARIES}  
										${OpenCV_LIBS})
add_executable(get_radar_data src/get_radar_data.cpp src/frontal_delphi_radar.cpp)
target_link_libraries(get_radar_data ${catkin_LIBRARIES}  ${Boost_LIBRARIES})

add_executable(get_ECU src/getECU.cpp src/AnalysisECU.cpp)
target_link_libraries(get_ECU ${catkin_LIBRARIES} ${Boost_LIBRARIES})
##############################
###### install packages #########
##############################
install(DIRECTORY launch DESTINATION share/frontal_delphi_radar/)

install(TARGETS frontal_delphi_radar get_radar_data get_ECU
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(
  TARGETS frontal_delphi_radar get_radar_data get_ECU
  EXPORT frontal_delphi_radarExport
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_GLOBAL_BIN_DESTINATION}
)

set(CUDA_USE_STATIC_CUDA_RUNTIME OFF)
