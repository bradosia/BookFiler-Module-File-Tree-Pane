cmake_minimum_required(VERSION 3.0)

set(PROJECT_NAME BookFiler-Module-File-Tree-Pane)
project(${PROJECT_NAME} VERSION 1.0.0 LANGUAGES CXX)
set(RELEASE_DIR "${CMAKE_BINARY_DIR}/${PROJECT_NAME}/")
set (CMAKE_RUNTIME_OUTPUT_DIRECTORY ${RELEASE_DIR})

# Set up source files
set(SOURCES
  src/Module.cpp
  src/UI/MainWidget.cpp
  src/UI/WidgetModel.cpp
  src/UI/WidgetItemDelegate.cpp
)

set(HEADERS
  src/Module.hpp
  src/UI/MainWidget.hpp
  src/UI/WidgetModel.hpp
  src/UI/WidgetItemDelegate.hpp
  src/core/config.hpp

  include/BookFiler-Module-File-Tree-Pane/Interface.hpp
)

set(SHARED_COMPILE_DEFINITIONS
  -DSHARED_LIBRARY
)

set(STATIC_COMPILE_DEFINITIONS
  -DSTATIC_LIBRARY
)

set(LIBRARIES ${LIBRARIES}
    # QT5
    Qt5::Widgets
)

if(WIN32)
    set(LIBRARIES ${LIBRARIES}
        # Windows Libraries
    )
elseif(UNIX)
    set(LIBRARIES ${LIBRARIES}
        # Unix Libraries
    )
endif()

set(SHARED_LINK_LIBRARIES ${LIBRARIES})
set(STATIC_LINK_LIBRARIES ${LIBRARIES})

set(CMAKE_CXX_STANDARD 17)

# Set up library names
if(BUILD_SHARED_LIBS)
  set(lib_shared_name ${PROJECT_NAME}LibShared)
  set(lib_shared_name_decorated ${lib_shared_name}${JKQtPlotter_LIBNAME_ADDITION})
  set(${PROJECT_NAME}_SHARED_LIBRARY ${lib_shared_name_decorated}  CACHE STRING "name of static library for ${PROJECT_NAME}" FORCE)
  message( STATUS "-- Building ${lib_shared_name}" )
endif(BUILD_SHARED_LIBS)

if(BUILD_STATIC_LIBS)
  set(lib_name ${PROJECT_NAME}Lib)
  set(lib_name_decorated ${lib_name}${JKQtPlotter_LIBNAME_ADDITION})
  set(${PROJECT_NAME}_STATIC_LIBRARY ${lib_name_decorated}  CACHE STRING "name of static library for ${PROJECT_NAME}" FORCE)
  message( STATUS "-- Building ${lib_name}" )
endif(BUILD_STATIC_LIBS)

set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)

include(CMakePackageConfigHelpers)

if(BUILD_SHARED_LIBS)
  add_library(${lib_shared_name} SHARED  ${SOURCES} ${HEADERS})
  target_compile_features(${lib_shared_name} PUBLIC cxx_std_17)
  set_property(TARGET ${lib_shared_name} PROPERTY VERSION "${PROJECT_VERSION}")
  set_property(TARGET ${lib_shared_name} PROPERTY OUTPUT_NAME  "${lib_shared_name_decorated}")
  target_include_directories(${lib_shared_name} PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/../>
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
  )
  set_target_properties(${lib_shared_name} PROPERTIES WINDOWS_EXPORT_ALL_SYMBOLS "ON")
  write_basic_package_version_file(${CMAKE_CURRENT_BINARY_DIR}/${lib_shared_name}Version.cmake
    VERSION ${PROJECT_VERSION}
  COMPATIBILITY AnyNewerVersion )

  # Compile Definitions
  target_compile_definitions(${lib_shared_name} PUBLIC ${SHARED_COMPILE_DEFINITIONS})
  # Libraries to link
  target_link_libraries(${lib_shared_name} PUBLIC ${SHARED_LINK_LIBRARIES})
endif()

if(BUILD_STATIC_LIBS)
  add_library(${lib_name} STATIC  ${SOURCES} ${HEADERS})
  target_compile_features(${lib_name} PUBLIC cxx_std_17)
  set_property(TARGET ${lib_name} PROPERTY VERSION "${PROJECT_VERSION}")
  set_property(TARGET ${lib_name} PROPERTY OUTPUT_NAME  "${lib_name_decorated}")
  target_include_directories(${lib_name} PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/../>
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
  )
  write_basic_package_version_file(${CMAKE_CURRENT_BINARY_DIR}/${lib_name}Version.cmake
    VERSION ${PROJECT_VERSION}
  COMPATIBILITY AnyNewerVersion )

  # Compile Definitions
  target_compile_definitions(${lib_name} PUBLIC ${STATIC_COMPILE_DEFINITIONS})
  # Libraries to link
  target_link_libraries(${lib_name} PUBLIC ${STATIC_LINK_LIBRARIES})
endif()

# Post build
if(BUILD_SHARED_LIBS)
    add_custom_command(TARGET ${lib_shared_name} POST_BUILD
        COMMAND ${CMAKE_COMMAND} -E make_directory "${CMAKE_BINARY_DIR}/modules/")
    add_custom_command(TARGET ${lib_shared_name} POST_BUILD
        COMMAND ${CMAKE_COMMAND} -E copy $<TARGET_FILE:${lib_shared_name}> "${CMAKE_BINARY_DIR}/modules/")
endif(BUILD_SHARED_LIBS)

# Installation
if(BUILD_SHARED_LIBS)
  message(STATUS "INSTALL: ${lib_shared_name} TO root=${CMAKE_BINARY_DIR} and current=${CMAKE_CURRENT_BINARY_DIR}")
  install(TARGETS ${lib_shared_name}
    RUNTIME DESTINATION "${CMAKE_INSTALL_PREFIX}/${BIN_NAME}/modules"
    ARCHIVE DESTINATION "${CMAKE_INSTALL_PREFIX}/${BIN_NAME}/modules"
    LIBRARY DESTINATION "${CMAKE_INSTALL_PREFIX}/${BIN_NAME}/modules"
  )
endif(BUILD_SHARED_LIBS)

if(BUILD_STATIC_LIBS)
  install(TARGETS ${lib_name} EXPORT ${lib_name}_TARGETS
    RUNTIME DESTINATION "${CMAKE_INSTALL_PREFIX}/${BIN_NAME}/lib"
    ARCHIVE DESTINATION "${CMAKE_INSTALL_PREFIX}/${BIN_NAME}/lib"
    LIBRARY DESTINATION "${CMAKE_INSTALL_PREFIX}/${BIN_NAME}/lib"
  )
endif(BUILD_STATIC_LIBS)

install(FILES ${HEADERS}
  DESTINATION "${CMAKE_INSTALL_PREFIX}/${BIN_NAME}/headers"
COMPONENT Headers)

install(FILES "${CMAKE_CURRENT_BINARY_DIR}/${lib_name}_Readme.txt" DESTINATION "${CMAKE_INSTALL_PREFIX}/${BIN_NAME}/doc/")
install(FILES "${CMAKE_SOURCE_DIR}/LICENSE" DESTINATION "${CMAKE_INSTALL_PREFIX}/${BIN_NAME}/doc/" RENAME "${lib_name}_LICENSE.txt" )
