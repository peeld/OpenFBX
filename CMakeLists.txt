cmake_minimum_required(VERSION 3.11)

project(OpenFBX LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 11)

include(GNUInstallDirs)
include(FetchContent)

FetchContent_Declare(
        libdeflate
        GIT_REPOSITORY https://github.com/ebiggers/libdeflate.git
        GIT_TAG master
)
FetchContent_MakeAvailable(libdeflate)

add_library(OpenFBX SHARED src/ofbx.cpp)
target_link_libraries(OpenFBX PRIVATE libdeflate_static)

target_include_directories(OpenFBX
        PUBLIC
        $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>
)

target_compile_definitions(OpenFBX PRIVATE _LARGEFILE64_SOURCE)
set_target_properties(OpenFBX PROPERTIES
        WINDOWS_EXPORT_ALL_SYMBOLS ON
        POSITION_INDEPENDENT_CODE ON)

install(TARGETS OpenFBX EXPORT openfbxTargets
        RUNTIME DESTINATION bin
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        INCLUDES DESTINATION include)

include(CMakePackageConfigHelpers)

set(INSTALL_CMAKE_DIR "${CMAKE_INSTALL_LIBDIR}/cmake/openfbx")

configure_package_config_file(
        openfbxConfig.cmake.in
        "${CMAKE_CURRENT_BINARY_DIR}/openfbxConfig.cmake"
        INSTALL_DESTINATION "${INSTALL_CMAKE_DIR}"
)

install(
        FILES "${CMAKE_CURRENT_BINARY_DIR}/openfbxConfig.cmake"
        DESTINATION "${INSTALL_CMAKE_DIR}"
)

install(
        EXPORT openfbxTargets
        FILE openfbxTargets.cmake
        DESTINATION "${INSTALL_CMAKE_DIR}"
)

install(FILES ${CMAKE_SOURCE_DIR}/src/ofbx.h
        DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)
