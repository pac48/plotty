cmake_minimum_required(VERSION 3.20.1)
project(plotty)

find_package(PythonLibs)
include_directories(${PYTHON_INCLUDE_DIRS})

add_definitions(-DPYTHON_VERSION_MAJOR=${PYTHON_VERSION_MAJOR})

set(HEADERS
    include/plotty/matplotlibcpp.hpp
  )

set(SOURCES
    src/matplotlibcpp.cpp
  )

find_package(glog REQUIRED)
find_package(Eigen3 REQUIRED)
include_directories(${EIGEN3_INCLUDE_DIRS})


add_library(plotty ${SOURCES} ${HEADERS})
target_link_libraries(plotty ${PYTHON_LIBRARY} Eigen3::Eigen glog::glog)
target_include_directories(plotty PRIVATE include)

# example test
add_executable(mpl_example_node src/mpl_example_node.cpp)
target_link_libraries(mpl_example_node plotty)
target_include_directories(mpl_example_node PRIVATE include)


# install
set(plotty_INSTALL_PREFIX /usr/local)
install(TARGETS plotty 
	EXPORT export_${PROJECT_NAME}
	DESTINATION ${plotty_INSTALL_PREFIX}/lib)
install(FILES include/plotty/matplotlibcpp.hpp DESTINATION ${plotty_INSTALL_PREFIX}/include/plotty)

configure_file(
    ${CMAKE_CURRENT_SOURCE_DIR}/cmake/${PROJECT_NAME}Config.cmake.in
    "${PROJECT_BINARY_DIR}/${CMAKE_FILES_DIRECTORY}/${PROJECT_NAME}Config.cmake" @ONLY
)


install(
    FILES
    "${PROJECT_BINARY_DIR}/${CMAKE_FILES_DIRECTORY}/${PROJECT_NAME}Config.cmake"
    #"${PROJECT_BINARY_DIR}/${PROJECT_NAME}ConfigVersion.cmake"
    DESTINATION ${plotty_INSTALL_PREFIX}/share/${PROJECT_NAME}
)

# Export targets.
set(export_targets ${export_targets};${PROJECT_NAME})
export(
    EXPORT export_${PROJECT_NAME}
    FILE "${PROJECT_BINARY_DIR}/${PROJECT_NAME}Targets.cmake"
    NAMESPACE ${PROJECT_NAME}::
)

install(
    EXPORT export_${PROJECT_NAME}
    DESTINATION ${plotty_INSTALL_PREFIX}/share/${PROJECT_NAME}
    FILE ${PROJECT_NAME}Targets.cmake
    NAMESPACE ${PROJECT_NAME}::
)
