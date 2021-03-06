cmake_minimum_required(VERSION 2.8.3)
project(visual_servo_control)

## Compile as C++11, supported in ROS Kinetic and newer
set(CMAKE_BUILD_TYPE "Release")
set(CMAKE_CXX_FLAGS "-std=c++11 -march=native -DEIGEN_DONT_PARALLELIZE")
set(CMAKE_CXX_FLAGS_RELEASE "-O3 -Wall")

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED COMPONENTS
    geometry_msgs
    roscpp
    std_msgs
    rm_cv
    camera_model
    dynamic_reconfigure
)

## System dependencies are found with CMake's conventions
find_package(Eigen3 REQUIRED)

###################################
## catkin specific configuration ##
###################################
## The catkin_package macro generates cmake config files for your package
## Declare things to be passed to dependent projects
## INCLUDE_DIRS: uncomment this if your package contains header files
## LIBRARIES: libraries you create in this project that dependent projects also need
## CATKIN_DEPENDS: catkin_packages dependent projects also need
## DEPENDS: system dependencies of this project that dependent projects also need
generate_dynamic_reconfigure_options(
        cfg/tuning.cfg
)

catkin_package(
#  INCLUDE_DIRS include
#  LIBRARIES gimbal_controller
   CATKIN_DEPENDS dynamic_reconfigure geometry_msgs roscpp std_msgs rm_cv
#  DEPENDS system_lib vertice
)


###########
## Build ##
###########

## Specify additional locations of header files
## Your package locations should be listed before other locations
include_directories(
    include
    include/kalman
    ${catkin_INCLUDE_DIRS}
    ${EIGEN3_INCLUDE_DIR}
)

## Add cmake target dependencies of the library
## as an example, code may need to be generated before libraries
## either from message generation or dynamic reconfigure
# add_dependencies(${PROJECT_NAME} ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
set(visual_servo_LIB_SOURCE_FILES
    ${PROJECT_SOURCE_DIR}/src/VisualServoController.cpp
)

## Declare a C++ executable
## With catkin_make all packages are built within a single CMake context
## The recommended prefix ensures that target names across packages don't collide
add_executable(visual_servo_controller
    src/visual_servo_controller.cpp
    ${visual_servo_LIB_SOURCE_FILES}
)

## Rename C++ executable without prefix
## The above recommended prefix causes long target names, the following renames the
## target back to the shorter version for ease of user use
## e.g. "rosrun someones_pkg node" instead of "rosrun someones_pkg someones_pkg_node"
# set_target_properties(${PROJECT_NAME}_node PROPERTIES OUTPUT_NAME node PREFIX "")

## Add cmake target dependencies of the executable
## same as for the library above
# add_dependencies(${PROJECT_NAME}_node ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})

## Specify libraries to link a library or executable target against
target_link_libraries(visual_servo_controller
    ${catkin_LIBRARIES}
)


add_executable(controller_tester
    src/controller_tester.cpp
)

target_link_libraries(controller_tester
    ${catkin_LIBRARIES}
)

## 20181022 new controller with feedforward
set(Kalman_filter_LIB_SOURCE_FILES
        ${PROJECT_SOURCE_DIR}/src/kalman/kalman.cpp
        )

set(VISUAL_SERVO_FEEDFORWARD_LIB_SOURCE_FILES
    ${Kalman_filter_LIB_SOURCE_FILES}
    ${PROJECT_SOURCE_DIR}/src/VisualServoControllerWithFeedForward.cpp
)

add_executable(visual_servo_controller_with_feedforward
    src/visual_servo_controller_with_feedforward.cpp
    ${VISUAL_SERVO_FEEDFORWARD_LIB_SOURCE_FILES}
)

target_link_libraries(visual_servo_controller_with_feedforward
    ${catkin_LIBRARIES}
)

add_dependencies(visual_servo_controller_with_feedforward ${PROJECT_NAME}_gencfg)

## 20181029 visual servo controller with wheel command
set(FIR_filter_LIB_SOURCE_FILES
        ${PROJECT_SOURCE_DIR}/src/FIR_filter/filt.cpp
        )

set(Kalman_filter_LIB_SOURCE_FILES
        ${PROJECT_SOURCE_DIR}/src/kalman/kalman.cpp
        )

set(III_VISUAL_SERVO_LIB_SOURCE_FILES
        ${Kalman_filter_LIB_SOURCE_FILES}
        ${PROJECT_SOURCE_DIR}/src/III_VisualServoController.cpp
        )

add_executable(III_visual_servo_with_wheel
        src/III_visual_servo_with_wheel.cpp
        ${III_VISUAL_SERVO_LIB_SOURCE_FILES}
        ${FIR_filter_LIB_SOURCE_FILES}
        )

target_link_libraries(III_visual_servo_with_wheel
        ${catkin_LIBRARIES}
        )

add_dependencies(III_visual_servo_with_wheel ${PROJECT_NAME}_gencfg)
