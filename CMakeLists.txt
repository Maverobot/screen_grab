cmake_minimum_required(VERSION 2.8.3)
project(screen_grab)

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED COMPONENTS
  cv_bridge  # image_show
  dynamic_reconfigure
  image_transport
  nodelet
  roscpp
  roslint
  sensor_msgs
  std_msgs
)

## System dependencies are found with CMake's conventions
# find_package(Boost REQUIRED COMPONENTS system)
find_package(OpenCV REQUIRED COMPONENTS highgui)

## Uncomment this if the package has a setup.py. This macro ensures
## modules and global scripts declared therein get installed
## See http://ros.org/doc/api/catkin/html/user_guide/setup_dot_py.html
# catkin_python_setup()

roslint_cpp()

generate_dynamic_reconfigure_options(
  cfg/ScreenGrab.cfg
)

catkin_package(
#  INCLUDE_DIRS include
  LIBRARIES screen_grab
  CATKIN_DEPENDS nodelet roscpp sensor_msgs std_msgs
  DEPENDS OpenCV
)

include_directories(
  include
  ${catkin_INCLUDE_DIRS}
)

add_library(screen_grab
   src/screen_grab.cpp
)

add_executable(screen_grab_node src/screen_grab_node.cpp)
add_dependencies(screen_grab ${PROJECT_NAME}_gencfg)

target_link_libraries(screen_grab
   ${catkin_LIBRARIES}
   X11
)

if(catkin_EXPORTED_LIBRARIES)
  add_dependencies(screen_grab ${catkin_EXPORTED_LIBRARIES})
endif()

target_link_libraries(screen_grab_node
  screen_grab
)

add_library(image_show
   src/image_show.cpp
)

target_link_libraries(image_show
   ${catkin_LIBRARIES}
   ${OpenCV_LIBRARIES}
)

## Mark executables and/or libraries for installation
install(TARGETS
  image_show
  screen_grab_node
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

## Mark other files for installation (e.g. launch and bag files, etc.)
install(FILES
  nodelet_image_show.xml
  nodelet_screen_grab.xml
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)

install(DIRECTORY launch/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/launch
  PATTERN ".svn" EXCLUDE)

