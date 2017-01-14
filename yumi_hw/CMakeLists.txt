cmake_minimum_required(VERSION 2.8.3)
project(yumi_hw)

set(CMAKE_BUILD_TYPE Release)
find_package(catkin REQUIRED COMPONENTS
  cmake_modules
  control_toolbox
  controller_interface
  controller_manager
  hardware_interface
  joint_limits_interface
  kdl_parser
  realtime_tools
  roscpp
  std_msgs
  tf
  transmission_interface
  urdf
  simple_message
  message_generation
)

find_package(Boost REQUIRED COMPONENTS system thread)

# The definition is copied from the CMakeList for the simple_message package.
add_definitions(-DROS=1)           #build using ROS libraries
add_definitions(-DLINUXSOCKETS=1)  #build using LINUX SOCKETS libraries


## Generate services in the 'srv' folder
add_service_files(
  FILES
  YumiGrasp.srv
)
# Generate added messages and services with any dependencies listed here
generate_messages(
  DEPENDENCIES
  std_msgs
)

catkin_package(
  INCLUDE_DIRS include
  LIBRARIES ${PROJECT_NAME}
  CATKIN_DEPENDS cmake_modules control_toolbox controller_interface controller_manager hardware_interface joint_limits_interface kdl_parser realtime_tools roscpp std_msgs tf transmission_interface urdf simple_message
)

include_directories(
  include
  ${catkin_INCLUDE_DIRS}
)

#add_library(${PROJECT_NAME}
#  src/yumi_hw.cpp
#  src/yumi_hw_rapid.cpp
#)

add_executable(yumi_hw_ifce_node
  src/yumi_hw_ifce_node.cpp
  src/yumi_hw.cpp
  src/yumi_hw_rapid.cpp)

add_executable(yumi_gripper_node src/yumi_gripper_node.cpp)

add_dependencies(yumi_gripper_node ${PROJECT_NAME}_generate_messages_cpp)

## Specify libraries to link a library or executable target against
#target_link_libraries( ${PROJECT_NAME} ${catkin_LIBRARIES} )
target_link_libraries(yumi_hw_ifce_node ${catkin_LIBRARIES} simple_message)
target_link_libraries(yumi_gripper_node ${catkin_LIBRARIES} simple_message)

install(TARGETS yumi_hw_ifce_node yumi_gripper_node
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.h"
)

install(DIRECTORY launch/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)

install(DIRECTORY rapid/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)
