CMAKE_MINIMUM_REQUIRED(VERSION 2.8)
set(LIB_NAME "flexrayusbinterface")
PROJECT ( ${LIB_NAME} )

set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/bin)

set(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/../flexrayusbinterface/cmake)

SET(CMAKE_BUILD_TYPE Debug)

SET(CMAKE_CXX_FLAGS "-std=c++11")
#==============================================================================
# Dependencies
#==============================================================================
#find_package(catkin REQUIRED COMPONENTS)
#find_package (PkgConfig REQUIRED)

# Boost
find_package ( Boost COMPONENTS system REQUIRED  )

# libftd2xx driver lib
find_package(libftd2xx REQUIRED)

# Mapbox
find_package ( Mapbox REQUIRED )

#==============================================================================
# Application
#==============================================================================
add_library(lib${LIB_NAME} src/FlexRayHardwareInterface.cpp src/Spi.cpp src/UsbChannel.cpp)

target_include_directories(
    lib${LIB_NAME}
	PRIVATE include
    PUBLIC ${libftd2xx_INCLUDE_DIRS}
    PUBLIC ${Boost_INCLUDE_DIR}
    PUBLIC ${Mapbox_INCLUDE_DIRS}
)

target_link_libraries(
    lib${LIB_NAME}
    ${Boost_LIBRARIES}
    ${libftd2xx_LIBRARY}
    -ldl
)

#==============================================================================
# Installation
#==============================================================================
file(GLOB LIB_INCLUDES include/flexrayusbinterface/*.*)
configure_file(cmake/config.cmake.in "${PROJECT_BINARY_DIR}/${LIB_NAME}Config.cmake" @ONLY)

install(FILES ${LIB_INCLUDES} DESTINATION include/${LIB_NAME})
install(TARGETS lib${LIB_NAME} EXPORT ${LIB_NAME}-targets DESTINATION lib/${LIB_NAME})
install(EXPORT ${LIB_NAME}-targets DESTINATION lib/${LIB_NAME})
install(FILES ${PROJECT_BINARY_DIR}/${LIB_NAME}Config.cmake DESTINATION lib/${LIB_NAME})

#==============================================================================
# Testing
#==============================================================================
OPTION (BUILD_TESTS "Build Unit Tests (Requires GTest)" OFF)
if(BUILD_TESTS)
	find_package(GTest REQUIRED)
	file(GLOB TEST_SOURCE_FILES test/*.cpp)
	include_directories(${GTEST_INCLUDE_DIRS})
	add_executable(unit_tests ${TEST_SOURCE_FILES})
	target_link_libraries(unit_tests libflexrayusbinterface ${GTEST_BOTH_LIBRARIES} pthread)
endif(BUILD_TESTS)