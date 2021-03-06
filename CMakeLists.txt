cmake_minimum_required(VERSION 2.8.3)
project(yei_tss_usb)

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED COMPONENTS diagnostic_updater roscpp sensor_msgs std_srvs gencpp std_msgs tf)

## System dependencies are found with CMake's conventions
find_package(libxml2 REQUIRED)

## Uncomment this if the package has a setup.py. This macro ensures
## modules and scripts declared therein get installed
# catkin_python_setup()

#######################################
## Declare ROS messages and services ##
#######################################

## Generate messages in the 'msg' folder
# add_message_files(
#   FILES
#   Message1.msg
#   Message2.msg
# )

## Generate services in the 'srv' folder
add_service_files(
  FILES
  LEDColor.srv
)

## Generate added messages and services with any dependencies listed here
generate_messages(
  DEPENDENCIES
  std_msgs
)

###################################################
## Declare things to be passed to other projects ##
###################################################

## LIBRARIES: libraries you create in this project that dependent projects also need
## CATKIN_DEPENDS: catkin_packages dependent projects also need
## DEPENDS: system dependencies of this project that dependent projects also need
catkin_package(
   INCLUDE_DIRS include
   LIBRARIES tss_usb tss_usb_driver
   CATKIN_DEPENDS diagnostic_updater roscpp sensor_msgs std_srvs
#  DEPENDS system_lib
)

###########
## Build ##
###########

## Specify additional locations of header files
include_directories(include
  ${catkin_INCLUDE_DIRS}
  ${LIBXML2_INCLUDE_DIRS}
)

## Declare a cpp library
add_library(tss_usb
  src/tss_usb.c
)
add_library(tss_usb_driver
  src/tss_usb_driver.cpp
)

## Declare a cpp executable
add_executable(tss_usb_node src/tss_usb_node.cpp)
add_executable(tss_usb_fw_updater src/tss_usb_fw_updater.c)

## Add dependencies to the executable
add_dependencies(tss_usb_driver ${PROJECT_NAME}_gencpp)
add_dependencies(tss_usb_node ${PROJECT_NAME}_gencpp)

## Specify libraries to link a library or executable target against
target_link_libraries(tss_usb_driver
  tss_usb
)
target_link_libraries(tss_usb_node
  tss_usb_driver
  ${catkin_LIBRARIES}
)
target_link_libraries(tss_usb_fw_updater
  tss_usb
  ${LIBXML2_LIBRARIES}
)

#############
## Install ##
#############

## Mark executable scripts (Python etc.) for installation
## not required for python when using catkin_python_setup()
# install(PROGRAMS
#   scripts/my_python_script
#   DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )

## Mark executables and/or libraries for installation
install(TARGETS tss_usb tss_usb_driver tss_usb_node
# ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

## Mark cpp header files for installation
install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.h" PATTERN "*.hpp"
  PATTERN ".svn" EXCLUDE
)

## Mark other files for installation (e.g. launch and bag files, etc.)
# install(FILES
#   # myfile1
#   # myfile2
#   DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
# )

#############
## Testing ##
#############

## Add gtest based cpp test target and link libraries
# catkin_add_gtest(${PROJECT_NAME}-test test/test_yei_tss_usb.cpp)
# if(TARGET ${PROJECT_NAME}-test)
#   target_link_libraries(${PROJECT_NAME}-test ${PROJECT_NAME})
# endif()

## Add folders to be run by python nosetests
# catkin_add_nosetests(test)
