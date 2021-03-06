CMAKE_MINIMUM_REQUIRED (VERSION 3.1)

################################
# ORB_SLAM2
################################


SET(ORB_SLAM2_LIB "ORB-SLAM2")

IF(NOT PROJECT_NAME)
  PROJECT(${ORB_SLAM2_LIB})
ENDIF(NOT PROJECT_NAME)

IF(NOT CMAKE_BUILD_TYPE)
  SET(CMAKE_BUILD_TYPE Release)
ENDIF()

SET(CMAKE_CXX_STANDARD 11)
SET(CMAKE_CXX_STANDARD_REQUIRED ON)

# TODO(nidgen) discuss/check if following is needed: (from original CMakeLists.txt)
####################
# MESSAGE("Build type: " ${CMAKE_BUILD_TYPE})

SET(CMAKE_C_FLAGS "${CMAKE_C_FLAGS}  -w")
SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -w")
# # TODO(gocarlos): read the warning, or put the -w in the package g2o from where most  warning come.

# LIST(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake_modules)
##################################################################

################################
# File Globbing
################################

FILE(GLOB_RECURSE ORB_SLAM_HEADERS "${CMAKE_CURRENT_LIST_DIR}/include/*.h")
FILE(GLOB_RECURSE ORB_SLAM_SOURCES "${CMAKE_CURRENT_LIST_DIR}/src/*.cc")

SET(ORB_SLAM_RESOURCES ${ORB_SLAM_SOURCES} ${ORB_SLAM_HEADERS})

################################
# Target Definitions
################################

ADD_LIBRARY(${ORB_SLAM2_LIB} STATIC ${ORB_SLAM_RESOURCES})

TARGET_INCLUDE_DIRECTORIES(${ORB_SLAM2_LIB} PUBLIC
  "${CMAKE_CURRENT_LIST_DIR}/include"
  "${CMAKE_CURRENT_LIST_DIR}/Thirdparty/g2o"
  "${CMAKE_CURRENT_LIST_DIR}/Thirdparty/DBoW2"
)

SET_TARGET_PROPERTIES(${ORB_SLAM2_LIB} PROPERTIES
  DEBUG_POSTFIX "_d"
  LIBRARY_OUTPUT_DIRECTORY ../lib/
  LIBRARY_OUTPUT_DIRECTORY_DEBUG ../lib/
  LIBRARY_OUTPUT_DIRECTORY_RELEASE ../lib/
  LIBRARY_OUTPUT_DIRECTORY_RELWITHDEBINFO ../lib/
  ARCHIVE_OUTPUT_DIRECTORY ../lib/
  ARCHIVE_OUTPUT_DIRECTORY_DEBUG ../lib/
  ARCHIVE_OUTPUT_DIRECTORY_RELWITHDEBINFO ../lib/
  ARCHIVE_OUTPUT_DIRECTORY_RELEASE ../lib/)


################################
# Dependencies
################################

FIND_PACKAGE(OpenCV REQUIRED)

# FIXME: this should be cleaned.
FIND_PACKAGE(Eigen3 3.1.0 QUIET)
IF(NOT Eigen3_FOUND)
  IF(EXISTS "/usr/include/eigen3")
    SET(EIGEN3_INCLUDE_DIR "/usr/include/eigen3")
  ELSEIF(EXISTS "/usr/local/include/eigen3")
    SET(EIGEN3_INCLUDE_DIR "/usr/local/include/eigen3")
  ELSE()
    MESSAGE(FATAL_ERROR "Could not find the library Eigen!")
  ENDIF()
ENDIF()
INCLUDE_DIRECTORIES(${EIGEN3_INCLUDE_DIR})

INCLUDE(${CMAKE_CURRENT_LIST_DIR}/Thirdparty/DBoW2/CMakeLists.txt)
INCLUDE(${CMAKE_CURRENT_LIST_DIR}/Thirdparty/g2o/CMakeLists.txt)

# FIXME(nidegen): I uncommented this as gtest won't work with this on.
# IF(${PROJECT_NAME} EQUAL ${ORB_SLAM2_LIB})
  TARGET_LINK_LIBRARIES(${ORB_SLAM2_LIB}
    debug pthread optimized pthread
    debug ${OpenCV_LIBRARIES} optimized ${OpenCV_LIBRARIES}
    debug ${DBOW2_LIB} optimized ${DBOW2_LIB}
    debug ${G2O_LIB} optimized ${G2O_LIB}
  )
# ENDIF(${PROJECT_NAME} EQUAL ${ORB_SLAM2_LIB})

# # Uncomment to display all CMAKE variables:
# GET_CMAKE_PROPERTY(VARIABLE_NAMES VARIABLES)
# FOREACH (VARIABLE_NAME ${VARIABLE_NAMES})
#    MESSAGE(STATUS "${VARIABLE_NAME}=${${VARIABLE_NAME}}")
# ENDFOREACH()
