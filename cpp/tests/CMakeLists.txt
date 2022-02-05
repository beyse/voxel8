# Testing library
FetchContent_Declare(
  catch
  GIT_REPOSITORY https://github.com/catchorg/Catch2.git
  GIT_TAG        v3.0.0-preview3
)
FetchContent_MakeAvailable(catch)
# Adds Catch2::Catch2

# Tests need to be added as executables first
add_executable(test_voxel8 test_voxel8.cpp)

# I'm using C++17 in the test
target_compile_features(test_voxel8 PRIVATE cxx_std_17)

# set the path to the test data as variable TEST_DATA_DIR
get_filename_component(TEST_DATA_DIR "./test_data" ABSOLUTE)
# write it into the header filer
configure_file(test_data_path.h.in test_data_path.h @ONLY)
include_directories(${CMAKE_CURRENT_BINARY_DIR})

# add local include directories
target_include_directories(test_voxel8 PRIVATE ../include)

# add source files
file(GLOB SOURCE_LIST CONFIGURE_DEPENDS "../src/*/*.cpp")
target_sources(test_voxel8 PRIVATE ${SOURCE_LIST})

if (WIN32)
  set(CMAKE_CXX_FLAGS_RELEASE "/MT")
endif (WIN32)

if (UNIX)
  SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -pthread")
endif (UNIX)

# If you register a test, then ctest and make test will run it.
# You can also run examples and check the output, as well.
add_test(NAME voxel8_tests COMMAND test_voxel8) # Command can be a target
