cmake_minimum_required(VERSION 3.5)
project(ethz_apriltag2)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${PROJECT_SOURCE_DIR}/cmake/)

find_package(Eigen3 REQUIRED NO_MODULE)
find_package(OpenCV REQUIRED)

add_definitions(-fPIC -O3)

# Library
file(GLOB SOURCE_FILES "src/*.cc")
add_library(${PROJECT_NAME}  ${SOURCE_FILES})
target_link_libraries(${PROJECT_NAME} ${OpenCV_LIBS} ${catkin_LIBRARIES})
target_include_directories(${PROJECT_NAME}
        PUBLIC ${EIGEN3_INCLUDE_DIR}
        PRIVATE include
)

# Demo
if(NOT APPLE)
  add_executable(apriltags_demo src/example/apriltags_demo.cpp src/example/Serial.cpp)
  target_link_libraries(apriltags_demo ${PROJECT_NAME} v4l2)
endif()


