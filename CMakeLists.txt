cmake_minimum_required(VERSION 3.0)
project(plflib LANGUAGES CXX VERSION 5.7)

option(ENABLE_TESTS "Build tests" OFF)

include(GNUInstallDirs)

add_library(colony INTERFACE)
add_library(plflib::colony ALIAS colony)

target_include_directories(
    colony INTERFACE
    $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/include/plflib>
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)

if (${ENABLE_TESTS})
    enable_testing()

    add_executable(tests)
    target_sources(tests PRIVATE src/plf_colony_test_suite.cpp)
    target_link_libraries(tests PRIVATE colony)

    add_test(NAME colony_tests COMMAND tests)
endif()


set(CONFIG_DEST ${CMAKE_INSTALL_LIBDIR}/cmake/plflib)

# Install project targets
install(
    TARGETS colony
    EXPORT plflibTargets
    DESTINATION ${CMAKE_INSTALL_LIBDIR}
)

# Install exports to those targets
install(
    EXPORT plflibTargets
    NAMESPACE plflib::
    DESTINATION ${CONFIG_DEST}
)

# Install our include directory
install(
  DIRECTORY "include/"
  DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}"
)

include(CMakePackageConfigHelpers)

configure_package_config_file(
    ${CMAKE_CURRENT_LIST_DIR}/plflibConfig.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/plflibConfig.cmake
    INSTALL_DESTINATION ${CONFIG_DEST}
)

write_basic_package_version_file(
  "${CMAKE_CURRENT_BINARY_DIR}/plflibConfigVersion.cmake"
  COMPATIBILITY SameMajorVersion
)

# version and config are placed in binary directory, but we want them in
# installed directory.
install(
  FILES
    "${CMAKE_CURRENT_BINARY_DIR}/plflibConfig.cmake"
    "${CMAKE_CURRENT_BINARY_DIR}/plflibConfigVersion.cmake"
  DESTINATION ${CONFIG_DEST}
)

