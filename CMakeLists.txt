cmake_minimum_required(VERSION 2.8.8)
set(PROJECT_NAME_STR simpleproject)
project(${PROJECT_NAME_STR} C CXX)

# command line arguments
option(BUILD_TESTS "Tests building" ON)

if(NOT DEFINED CMAKE_BUILD_TYPE)
   set(CMAKE_BUILD_TYPE Debug)
endif()

# set common variables
set(PROJECT_BIN_DIR ${PROJECT_SOURCE_DIR}/bin)
set(PROJECT_BIN_DIR_DEBUG ${PROJECT_BIN_DIR}/debug)
set(PROJECT_BIN_DIR_RELEASE ${PROJECT_BIN_DIR}/release)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_DEBUG   ${PROJECT_BIN_DIR_DEBUG})
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY_DEBUG   ${PROJECT_BIN_DIR_DEBUG})
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY_DEBUG   ${PROJECT_BIN_DIR_DEBUG})
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_RELEASE ${PROJECT_BIN_DIR_RELEASE})
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY_RELEASE ${PROJECT_BIN_DIR_RELEASE})
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY_RELEASE ${PROJECT_BIN_DIR_RELEASE})

# set compilers options
if(CMAKE_CXX_COMPILER_ID MATCHES "GNU")
   add_definitions(-Wno-deprecated -std=c++11)
   set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -O3")
elseif(CMAKE_CXX_COMPILER_ID MATCHES "MSVC")
   add_definitions(/EHsc)
   set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} /Ox")
endif()

# set common include folder for project
set(COMMON_INCLUDES ${PROJECT_SOURCE_DIR}/src)

# build main library
include_directories(${COMMON_INCLUDES})
file(GLOB SRC_FILES ${PROJECT_SOURCE_DIR}/src/*.cpp)
add_library(${PROJECT_NAME_STR} ${SRC_FILES})

# build tests
if(BUILD_TESTS)
   set(TEST_FRAMEWORK_NAME UnitTest++)
   set(UNIT_CPP_INCLUDE ${PROJECT_SOURCE_DIR}/ext/unittest-cpp/UnitTest++)
   add_subdirectory(${PROJECT_SOURCE_DIR}/ext/unittest-cpp)

   include_directories(${COMMON_INCLUDES} ${UNIT_CPP_INCLUDE})
   file(GLOB SRC_TEST_FILES ${PROJECT_SOURCE_DIR}/test/*.cpp)
   add_executable(${PROJECT_NAME_STR}_test ${SRC_TEST_FILES})
   target_link_libraries(${PROJECT_NAME_STR}_test ${TEST_FRAMEWORK_NAME} ${PROJECT_NAME_STR})
endif()
