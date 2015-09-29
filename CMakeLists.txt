cmake_minimum_required(VERSION 2.8.8)
set(PROJECT_NAME simpleproject)
project(${PROJECT_NAME} C CXX)

# command line arguments
option(BUILD_TESTS "Tests building flag" ON)

if(NOT DEFINED CMAKE_BUILD_TYPE)
   set(CMAKE_BUILD_TYPE Debug)
endif()

# set common variables
set(PROJECT_BIN_DIR ${PROJECT_SOURCE_DIR}/bin)
set(PROJECT_LIB_DIR ${PROJECT_SOURCE_DIR}/lib)
set(PROJECT_BIN_DIR_DEBUG ${PROJECT_BIN_DIR}/debug)
set(PROJECT_LIB_DIR_DEBUG ${PROJECT_LIB_DIR}/debug)
set(PROJECT_BIN_DIR_RELEASE ${PROJECT_BIN_DIR}/release)
set(PROJECT_LIB_DIR_RELEASE ${PROJECT_LIB_DIR}/release)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_DEBUG   ${PROJECT_BIN_DIR_DEBUG})
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY_DEBUG   ${PROJECT_BIN_DIR_DEBUG})
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY_DEBUG   ${PROJECT_LIB_DIR_DEBUG})
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_RELEASE ${PROJECT_BIN_DIR_RELEASE})
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY_RELEASE ${PROJECT_BIN_DIR_RELEASE})
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY_RELEASE ${PROJECT_LIB_DIR_RELEASE})

# set compilers options
if(CMAKE_CXX_COMPILER_ID MATCHES "GNU")
   add_definitions(-Wno-deprecated -std=c++11)
   set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -O3")
elseif(CMAKE_CXX_COMPILER_ID MATCHES "MSVC")
   add_definitions(/EHsc)
   set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} /Ox")
endif()

# set common include folder for project
set(SOURCES_DIR ${PROJECT_SOURCE_DIR}/src)
set(COMMON_INCLUDES ${SOURCES_DIR})

# build main library
include_directories(${COMMON_INCLUDES})
file(GLOB SRC_FILES ${SOURCES_DIR}/*.cpp)
file(GLOB SRC_EX_FILES ${SOURCES_DIR}/*.cxx)
add_library(${PROJECT_NAME}_static ${SRC_FILES})
add_executable(${PROJECT_NAME} ${SRC_EX_FILES})
target_link_libraries(${PROJECT_NAME} ${PROJECT_NAME}_static)

# build tests
if(BUILD_TESTS)
  set(GTEST_ROOT ${PROJECT_SOURCE_DIR}/ext/gtest)
  set(GTEST_INCLUDES ${GTEST_ROOT}/include)
  include_directories(${GTEST_INCLUDES} ${GTEST_ROOT})
  add_library(gtest ${GTEST_ROOT}/src/gtest-all.cc)

  include_directories(${COMMON_INCLUDES} ${GTEST_INCLUDES})
  file(GLOB SRC_TEST_FILES ${PROJECT_SOURCE_DIR}/test/*.cpp)
  add_executable(${PROJECT_NAME}_test ${SRC_TEST_FILES})
  target_link_libraries(${PROJECT_NAME}_test ${PROJECT_NAME}_static gtest)
endif()
