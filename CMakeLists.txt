cmake_minimum_required(VERSION 2.8.3)
project(sspp)
#SET(CMAKE_C_COMPILER /usr/bin/gcc-4.9)
#SET(CMAKE_CXX_COMPILER /usr/bin/g++-4.9)

set(CMAKE_BUILD_TYPE Release) #Debug or Release
add_definitions(-std=c++11)
add_definitions(-std=c++14)
SET(CMAKE_CXX_FLAGS "-frounding-math" )
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
# Warnings
add_definitions(-W -Wall -Wextra
  #-Wcast-qual
  -Wwrite-strings -Wunreachable-code -Wpointer-arith
  -Winit-self -Wredundant-decls
  -Wno-unused-parameter -Wno-unused-function)
add_definitions (-D_GLIBCXX_USE_CXX11_ABI=1)

find_package(catkin REQUIRED COMPONENTS
  roscpp
  pcl_ros
  sensor_msgs
  geometry_msgs
  tf
  tf_conversions
  eigen_conversions
  nav_msgs
  shape_msgs
  std_msgs
  rviz_visual_tools
  # octomap_world
  )

find_package(CGAL QUIET COMPONENTS)
find_package(PCL REQUIRED)
find_package(CUDA QUIET)
find_package(octomap REQUIRED)

add_service_files(
  FILES
  sspp_srv.srv
)

generate_messages(
  DEPENDENCIES
  geometry_msgs
  visualization_msgs
  std_msgs
)

if ( NOT CUDA_FOUND )
  message(STATUS "You will not be able to use coverage heuristic, it requires CUDA")
#  return()
endif()


include(FindPkgConfig)

catkin_package(
   INCLUDE_DIRS include
   LIBRARIES ${catkin_LIBRARIES} ${OCTOMAP_LIBRARIES} SSPathPlanner
   CATKIN_DEPENDS roscpp pcl_ros # octomap_world
)

include_directories(include
  ${catkin_INCLUDE_DIRS}
  ${rviz_visual_tools_INCLUDE_DIRS}
  ${OCTOMAP_INCLUDE_DIRS}
)

set(CMAKE_AUTOMOC ON)
set(CMAKE_INCLUDE_CURRENT_DIR ON)

# Set include directories
link_directories(${PCL_LIBRARY_DIRS})
add_definitions(${PCL_DEFINITIONS})

add_library(SSPathPlanner SHARED
    src/astar.cpp
    src/llist.cpp
    src/map.cpp
    src/node.cpp
    src/pathplanner.cpp
    src/robot.cpp
    src/searchspace.cpp
    src/searchspacenode.cpp
    src/ssppexception.cpp
    #src/distance_heuristic.cpp
    src/rviz_drawing_tools.cpp
    src/sensors.cpp
    )

target_link_libraries(SSPathPlanner
    ${OCTOMAP_LIBRARIES}
    )

#add_executable(distance_heuristic_test src/distance_heuristic_test.cpp)
#target_include_directories(distance_heuristic_test PUBLIC include ${catkin_INCLUDE_DIRS})
#add_dependencies(distance_heuristic_test ${catkin_EXPORTED_TARGETS})
#target_link_libraries(distance_heuristic_test SSPathPlanner
#    -lgmp -lmpfr
#    ${CGAL_LIBRARY}
#    ${CGAL_Core_LIBRARY}
#    ${catkin_LIBRARIES}
#    ${PCL_LIBRARIES}
#    ${rviz_visual_tools_LIBRARIES}
#    )

#add_executable(reactive_planner_test src/reactive_planner_test.cpp)
#target_include_directories(reactive_planner_test PUBLIC include ${catkin_INCLUDE_DIRS})
#add_dependencies(reactive_planner_test ${catkin_EXPORTED_TARGETS})
#target_link_libraries(reactive_planner_test SSPathPlanner
#    -lgmp -lmpfr
#    ${CGAL_LIBRARY}
 #   ${CGAL_Core_LIBRARY}
#    ${catkin_LIBRARIES}
#    ${PCL_LIBRARIES}
#    ${rviz_visual_tools_LIBRARIES}
#    )

#add_executable(reactive_planner_server_node src/reactive_planner_server_node.cpp src/reactive_planner_server.cpp)
#target_include_directories(reactive_planner_server_node PUBLIC include ${catkin_INCLUDE_DIRS})
#add_dependencies(reactive_planner_server_node ${catkin_EXPORTED_TARGETS})
#target_link_libraries(reactive_planner_server_node SSPathPlanner
#    -lgmp -lmpfr
#    ${CGAL_LIBRARY}
#    ${CGAL_Core_LIBRARY}
#    ${catkin_LIBRARIES}
#    ${PCL_LIBRARIES}
#    ${rviz_visual_tools_LIBRARIES}
 #   )

