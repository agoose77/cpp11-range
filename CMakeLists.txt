cmake_minimum_required(VERSION 3.11 FATAL_ERROR)

# set the project name and version
project(
  cpp11-range
  VERSION 0.1.0
  LANGUAGES CXX)

# Use standard GNU directories
include(GNUInstallDirs)
# Include macro to generate version file
include(CMakePackageConfigHelpers)

# Create target
add_library(${PROJECT_NAME} INTERFACE)
target_include_directories(
  ${PROJECT_NAME} INTERFACE $<INSTALL_INTERFACE:include>
                            $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/include>)
target_compile_features(${PROJECT_NAME} INTERFACE cxx_std_11)

# If this is the parent project
if(CMAKE_PROJECT_NAME STREQUAL PROJECT_NAME)
  # Allow user to disable tests
  option(BUILD_TESTING "Build the test suite" ON)
  if(BUILD_TESTING)
    add_subdirectory(tests)
  endif()
endif()

# Install targets to CMAKE_INSTALL_ directories and associate with an exported
# Targets file
install(TARGETS ${PROJECT_NAME} EXPORT ${PROJECT_NAME}Targets)
# Install the Targets file for usage by the Config file
install(
  EXPORT ${PROJECT_NAME}Targets
  FILE ${PROJECT_NAME}Targets.cmake
  NAMESPACE ${PROJECT_NAME}::
  DESTINATION lib/cmake/${PROJECT_NAME})

# Generate the Config file, only expanding @VAR@ form Config
configure_file(Config.cmake.in "${PROJECT_NAME}Config.cmake" @ONLY)
# Generate the ConfigVersion file
write_basic_package_version_file(
  "${PROJECT_NAME}ConfigVersion.cmake"
  VERSION ${PACKAGE_VERSION}
  COMPATIBILITY SameMajorVersion)
# Install the Config & ConfigVersion files will use the Targets file generated
# above
install(FILES "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake"
              "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}ConfigVersion.cmake"
        DESTINATION lib/cmake/${PROJECT_NAME})
# Copy include files to the install include directory
install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include/${PROJECT_NAME}
        DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}")

# Add alias to support add_subdirectory
add_library(${PROJECT_NAME}::${PROJECT_NAME} ALIAS ${PROJECT_NAME})
