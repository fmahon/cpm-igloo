if(APPLE)
  cmake_minimum_required(VERSION 2.8.11 FATAL_ERROR)
else()
  cmake_minimum_required(VERSION 2.8.7 FATAL_ERROR)
endif()

project(IglooTests)

#------------------------------------------------------------------------------
# Required CPM Setup - See: http://github.com/iauns/cpm
#------------------------------------------------------------------------------
set(CPM_DIR "${CMAKE_CURRENT_BINARY_DIR}/cpm-packages" CACHE TYPE STRING)
find_package(Git)
if(NOT GIT_FOUND)
  message(FATAL_ERROR "CPM requires Git.")
endif()
if ((NOT DEFINED CPM_MODULE_CACHE_DIR) AND (NOT "$ENV{CPM_CACHE_DIR}" STREQUAL ""))
  set(CPM_MODULE_CACHE_DIR "$ENV{CPM_CACHE_DIR}")
endif()
if ((NOT EXISTS ${CPM_DIR}/CPM.cmake) AND (DEFINED CPM_MODULE_CACHE_DIR))
  if (EXISTS "${CPM_MODULE_CACHE_DIR}/github_iauns_cpm")
    message(STATUS "Found cached version of CPM.")
    file(COPY "${CPM_MODULE_CACHE_DIR}/github_iauns_cpm/" DESTINATION ${CPM_DIR})
  endif()
endif()
if (NOT EXISTS ${CPM_DIR}/CPM.cmake)
  message(STATUS "Cloning repo (https://github.com/iauns/cpm)")
  execute_process(
    COMMAND "${GIT_EXECUTABLE}" clone https://github.com/iauns/cpm ${CPM_DIR}
    RESULT_VARIABLE error_code
    OUTPUT_QUIET ERROR_QUIET)
  if(error_code)
    message(FATAL_ERROR "CPM failed to get the hash for HEAD")
  endif()
endif()
include(${CPM_DIR}/CPM.cmake)

#-----------------------------------------------------------------------
# CPM Modules
#-----------------------------------------------------------------------
# ++ MODULE: igloo
CPM_AddModule("igloo"
  SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/..")

CPM_Finish()

set(TEST_DIR ${CMAKE_CURRENT_SOURCE_DIR}/../3rdParty/igloo/tests)

include_directories(${TEST_DIR})

file(GLOB IglooTestFiles ${TEST_DIR}/*.cpp ${TEST_DIR}/**/*.cpp)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_DEBUG ${CMAKE_CURRENT_SOURCE_DIR}/bin)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/bin)

add_executable(tests ${IglooTestFiles})
target_link_libraries(tests ${CPM_LIBRARIES})

