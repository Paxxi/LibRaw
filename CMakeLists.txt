cmake_minimum_required(VERSION 3.2)

project(libraw VERSION 0.18.0 LANGUAGES CXX)

if(MSVC)
  set(CMAKE_DEBUG_POSTFIX d)
endif()

add_library(libraw
  ${CMAKE_CURRENT_SOURCE_DIR}/src/libraw_c_api.cpp
  ${CMAKE_CURRENT_SOURCE_DIR}/src/libraw_cxx.cpp
  ${CMAKE_CURRENT_SOURCE_DIR}/src/libraw_datastream.cpp
  ${CMAKE_CURRENT_SOURCE_DIR}/libraw/libraw_alloc.h
  ${CMAKE_CURRENT_SOURCE_DIR}/libraw/libraw_const.h
  ${CMAKE_CURRENT_SOURCE_DIR}/libraw/libraw_datastream.h
  ${CMAKE_CURRENT_SOURCE_DIR}/libraw/libraw_internal.h
  ${CMAKE_CURRENT_SOURCE_DIR}/libraw/libraw_types.h
  ${CMAKE_CURRENT_SOURCE_DIR}/libraw/libraw_version.h
  ${CMAKE_CURRENT_SOURCE_DIR}/libraw/libraw.h
  ${CMAKE_CURRENT_SOURCE_DIR}/internal/dcraw_common.cpp
  ${CMAKE_CURRENT_SOURCE_DIR}/internal/dcraw_fileio.cpp
  ${CMAKE_CURRENT_SOURCE_DIR}/internal/demosaic_packs.cpp
)

target_include_directories(libraw
  PRIVATE
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/libraw>
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
  INTERFACE
  $<INSTALL_INTERFACE:include/libraw>
  PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/libraw>
)

target_compile_definitions(libraw
  PRIVATE
  _CRT_SECURE_NO_WARNINGS
  _CRT_STDC_NO_DEPRECATE
  _USRDLL
  QT_NO_DEBUG
  _WINDOWS
  UNICODE
  QT_LARGEFILE_SUPPORT
  LIBRAW_BUILDLIB
)

include(CMakePackageConfigHelpers)
write_basic_package_version_file(
  ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}-config-version.cmake
  VERSION ${PROJECT_VERSION}
  COMPATIBILITY AnyNewerVersion
)

install(TARGETS libraw EXPORT libraw
  RUNTIME DESTINATION bin
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
)

install(DIRECTORY
  ${CMAKE_CURRENT_SOURCE_DIR}/libraw
  DESTINATION include
)

install(EXPORT libraw
  FILE
  ${PROJECT_NAME}.cmake
  NAMESPACE
    ${PROJECT_NAME}::
  DESTINATION
    lib/cmake/${PROJECT_NAME}
)

install(FILES
  cmake/${PROJECT_NAME}-config.cmake
  ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}-config-version.cmake
  DESTINATION
    lib/cmake/${PROJECT_NAME}
)

export(TARGETS libraw
  FILE
  ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}-config.cmake
  NAMESPACE ${PROJECT_NAME}::
)

if(MSVC AND BUILD_SHARED_LIBS)
  install(FILES
    $<TARGET_PDB_FILE:${PROJECT_NAME}>
    DESTINATION lib
  )
endif()