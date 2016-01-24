cmake_minimum_required(VERSION 2.8.8)
set(PROJECT_NAME simpleproject)
project(${PROJECT_NAME} C CXX)

# command line arguments
option(BUILD_TESTS "Tests building flag" ON)

if(NOT DEFINED CMAKE_BUILD_TYPE)
   set(CMAKE_BUILD_TYPE Debug)
endif()

# set common variables
set(PROJECT_BIN_DIR ${PROJECT_SOURCE_DIR}/_out/bin)
set(PROJECT_LIB_DIR ${PROJECT_SOURCE_DIR}/_out/lib)
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
   set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -O2")
   set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -Od")
elseif(CMAKE_CXX_COMPILER_ID MATCHES "MSVC")
   add_definitions(/EHsc)
   set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} /Ox")
   set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} /Od")
endif()

# set project common variables
set(COMMON_SOURCES ${PROJECT_SOURCE_DIR}/src)
set(TEST_SOURCES ${PROJECT_SOURCE_DIR}/test)
set(COMON_INCLUDES ${COMMON_SOURCES})
set(COMON_TEST_INCLUDES ${TEST_SOURCES})
set(EX_MAIN_FILE ${COMMON_SOURCES}/main.cpp)
set(EX_MAIN_TEST_FILE ${TEST_SOURCES}/main.cpp)
set(GTEST_ROOT ${PROJECT_SOURCE_DIR}/ext/gtest)
set(GTEST_INCLUDES ${GTEST_ROOT}/include)

if(BUILD_TESTS)
  include_directories(${GTEST_INCLUDES} ${GTEST_ROOT})
  add_library(gtest ${GTEST_ROOT}/src/gtest-all.cc)
endif()

# enumerate project modules
set(EXAMPLE_MODULE_DIR ${PROJECT_SOURCE_DIR}/src/example)
set(EXAMPLE_MODULE_TEST_DIR ${PROJECT_SOURCE_DIR}/test/example)

# build example_module
include_directories(${COMON_INCLUDES} ${EXAMPLE_MODULE_DIR})
file(GLOB EXAMPLE_MODULE_SRC_FILES ${EXAMPLE_MODULE_DIR}/*.cpp)
add_library(example_module_static ${EXAMPLE_MODULE_SRC_FILES})

# build tests for exmaple_module
if(BUILD_TESTS)
  include_directories(${COMMON_INCLUDES} ${COMON_TEST_INCLUDES} ${EXAMPLE_MODULE_DIR} ${GTEST_INCLUDES})
  file(GLOB EXAMPLE_MODULE_TEST_FILES ${EXAMPLE_MODULE_TEST_DIR}/*.cpp)
  add_executable(example_module_test ${EX_MAIN_TEST_FILE} ${EXAMPLE_MODULE_TEST_FILES})
  target_link_libraries(example_module_test example_module_static gtest)
endif()

# add executable
include_directories(${COMON_INCLUDES} ${EXAMPLE_MODULE_DIR})
add_executable(${PROJECT_NAME} ${EX_MAIN_FILE})
target_link_libraries(${PROJECT_NAME} example_module_static)
