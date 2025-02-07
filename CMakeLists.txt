cmake_minimum_required(VERSION 3.16)

# Project name and version
project(SilverEngine VERSION 1.0 LANGUAGES CXX)

# Set C++ standard
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED True)

# Include GNUInstallDirs for better install path handling
include(GNUInstallDirs)

# Add the library (shared object)
add_library(silverengine SHARED src/silverengine.cpp)

# Set library versioning
set_target_properties(silverengine PROPERTIES VERSION ${PROJECT_VERSION} SOVERSION 1)

# Add compiler warnings for better development
if (CMAKE_CXX_COMPILER_ID MATCHES "GNU|Clang")
    target_compile_options(silverengine PRIVATE -Wall -Wextra -Wpedantic)
endif()

# Specify include directories for consumers of the library
target_include_directories(silverengine PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)

# Installation rules
install(TARGETS silverengine
    EXPORT silverengineTargets
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}       # .so files
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}       # Static library (if applicable)
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}       # Executables
    INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}  # Headers
)

# Install headers (only header files)
install(DIRECTORY include/ DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/silverengine FILES_MATCHING PATTERN "*.h")

# Create and install the package configuration files
include(CMakePackageConfigHelpers)

# Create version file
write_basic_package_version_file(
    "${CMAKE_CURRENT_BINARY_DIR}/silverengineConfigVersion.cmake"
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY AnyNewerVersion
)

# Create config file
configure_file(
    "${CMAKE_CURRENT_SOURCE_DIR}/cmake/silverengineConfig.cmake.in"
    "${CMAKE_CURRENT_BINARY_DIR}/silverengineConfig.cmake"
    @ONLY
)

# Install the config and version files
install(FILES
    "${CMAKE_CURRENT_BINARY_DIR}/silverengineConfig.cmake"
    "${CMAKE_CURRENT_BINARY_DIR}/silverengineConfigVersion.cmake"
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/silverengine
)

# Export the target for use by other projects
install(EXPORT silverengineTargets
    FILE silverengineTargets.cmake
    NAMESPACE silverengine::
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/silverengine
)

# Export the targets to a file, so they can be used by other projects
export(EXPORT silverengineTargets
    FILE "${CMAKE_CURRENT_BINARY_DIR}/silverengineTargets.cmake"
    NAMESPACE silverengine::
)
