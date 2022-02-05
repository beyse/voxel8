cmake_minimum_required(VERSION 3.14)

project(
  voxel8
  VERSION 0.1
  DESCRIPTION "Fast STL to Voxel Slicer in C++"
  LANGUAGES CXX)

add_definitions(-D_LIBCPP_DISABLE_AVAILABILITY)

if(CMAKE_PROJECT_NAME STREQUAL PROJECT_NAME)

  set(CMAKE_CXX_EXTENSIONS OFF)

  # Support folders in IDEs
  set_property(GLOBAL PROPERTY USE_FOLDERS ON)

  # Testing only available if this is the main app
  # Note this needs to be done in the main CMakeLists
  # since it calls enable_testing, which must be in the
  # main CMakeLists.
  include(CTest)

  # Docs only available if this is the main app
  find_package(Doxygen)
  if(Doxygen_FOUND)
    add_subdirectory(docs)
  else()
    message(STATUS "Doxygen not found, not building docs")
  endif()
endif()

# FetchContent added in CMake 3.11, downloads during the configure step
include(FetchContent)

# FetchContent_MakeAvailable was not added until CMake 3.14; use our shim
#if(${CMAKE_VERSION} VERSION_LESS 3.14)
#  include(cmake/add_FetchContent_MakeAvailable.cmake)
#endif()

add_executable(voxel8 src/main.cpp)
# add local include directories
target_include_directories(voxel8 PUBLIC include)
file(GLOB HEADER_LIST CONFIGURE_DEPENDS "include/*.h")
target_sources(voxel8 PUBLIC ${HEADER_LIST})
source_group(
  TREE "${PROJECT_SOURCE_DIR}/include"
  PREFIX "Header Files"
  FILES ${HEADER_LIST})
# add source files
file(GLOB SOURCE_LIST CONFIGURE_DEPENDS "src/*/*.cpp")
target_sources(voxel8 PRIVATE ${SOURCE_LIST})

if (WIN32)
  set(CMAKE_CXX_FLAGS_RELEASE "/MT")
endif (WIN32)

if (UNIX)
  SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -pthread")
endif (UNIX)

# Testing only available if this is the main app
# Emergency override VOXEL8_BUILD_TESTING provided as well
if((CMAKE_PROJECT_NAME STREQUAL PROJECT_NAME OR VOXEL8_BUILD_TESTING)
   AND BUILD_TESTING)
  add_subdirectory(tests)
endif()
