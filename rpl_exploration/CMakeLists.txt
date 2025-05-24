cmake_minimum_required(VERSION 2.8.3)
project(rpl_exploration)

add_definitions(-std=c++14)

find_package(catkin REQUIRED COMPONENTS
  roscpp
  rospy
  tf
  std_msgs
  sensor_msgs
  geometry_msgs
  octomap_msgs
  nodelet
  
  genmsg 
  actionlib_msgs 
  actionlib
  aeplanner
  aeplanner_evaluation
  rrtplanner

  quadrotor_msgs
)

find_package(octomap REQUIRED)
find_package(PCL REQUIRED)


add_action_files(DIRECTORY action FILES FlyTo.action)
generate_messages(DEPENDENCIES actionlib_msgs geometry_msgs)


catkin_package(
)


include_directories(${catkin_INCLUDE_DIRS})
include_directories(${OCTOMAP_INCLUDE_DIRS})
include_directories(${PCL_INCLUDE_DIRS})
add_definitions(${PCL_DEFINITIONS})


add_library(laserscan_to_pointcloud src/laserscan_to_pointcloud.cpp)
target_link_libraries(laserscan_to_pointcloud ${catkin_LIBRARIES})
if(catkin_EXPORTED_LIBRARIES)
  add_dependencies(laserscan_to_pointcloud ${catkin_EXPORTED_LIBRARIES})
endif()

add_executable(fly_to src/fly_to.cpp)
target_link_libraries( fly_to ${catkin_LIBRARIES})
add_dependencies(fly_to rpl_exploration_generate_messages_cpp)

add_executable(rpl_exploration src/rpl_exploration.cpp)
target_link_libraries(rpl_exploration ${catkin_LIBRARIES})
add_dependencies(rpl_exploration ${catkin_EXPORTED_TARGETS})
add_dependencies(rpl_exploration rpl_exploration_generate_messages_cpp)
