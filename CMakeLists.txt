cmake_minimum_required(VERSION 3.16)
project(ethz_apriltag2 VERSION 1.0.0)

find_package(Eigen3 REQUIRED NO_MODULE)
find_package(OpenCV REQUIRED)

# Library
file(GLOB SOURCE_FILES "src/*.cc")
add_library(${PROJECT_NAME} STATIC ${SOURCE_FILES})
target_link_libraries(${PROJECT_NAME} ${OpenCV_LIBS})
target_include_directories(${PROJECT_NAME}
    PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
        $<BUILD_INTERFACE:${EIGEN3_INCLUDE_DIR}>
        $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)

# Demo
if (BUILD_EXAMPLES)
    if(NOT APPLE)
        add_executable(apriltags_demo src/example/apriltags_demo.cpp src/example/Serial.cpp)
        target_link_libraries(apriltags_demo ${PROJECT_NAME} v4l2)
    endif()
endif()


# install library
set(version_config "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}ConfigVersion.cmake")
set(project_config "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake")
set(targets_export_name "${PROJECT_NAME}Targets")
set(config_install_dir "lib/cmake")
set(include_install_dir "include")
set(lib_install_dir "lib")


include(CMakePackageConfigHelpers)
configure_package_config_file(
    "${PROJECT_NAME}.cmake.in"
    "${project_config}"
    INSTALL_DESTINATION "${config_install_dir}"
)
write_basic_package_version_file("${version_config}" COMPATIBILITY SameMajorVersion)

install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include/
    DESTINATION ${include_install_dir}
    FILES_MATCHING PATTERN "*.h" PATTERN "*.hpp")
install(TARGETS ${PROJECT_NAME} EXPORT ${targets_export_name}
    LIBRARY DESTINATION ${lib_install_dir}
    INCLUDES DESTINATION ${include_install_dir}
)
install(EXPORT ${targets_export_name}
    NAMESPACE ethz_apriltag2::
    DESTINATION ${config_install_dir}
)
install(FILES ${project_config} ${version_config} DESTINATION ${config_install_dir})