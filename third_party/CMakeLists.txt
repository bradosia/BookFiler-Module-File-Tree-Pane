cmake_minimum_required(VERSION 3.11)
# The FetchModule is only available since CMake 3.11.

include(FetchContent)

FetchContent_GetProperties(Module-Manager)
if(NOT Module-Manager_POPULATED)
FetchContent_Declare(
    Module-Manager
    GIT_REPOSITORY "https://github.com/bradosia/Module-Manager.git"
    SOURCE_DIR "${PROJECT_SOURCE_DIR}/third_party/Module-Manager"
)
FetchContent_Populate(Module-Manager)
endif()

find_library(LIBRARY_EXISTS ModuleManager)
if(NOT LIBRARY_EXISTS)
add_subdirectory(Module-Manager)
endif()

FetchContent_GetProperties(Settings-Manager)
if(NOT Settings-Manager_POPULATED)
FetchContent_Declare(
    Settings-Manager
    GIT_REPOSITORY "https://github.com/bradosia/Settings-Manager.git"
    SOURCE_DIR "${PROJECT_SOURCE_DIR}/third_party/Settings-Manager"
)
FetchContent_Populate(Settings-Manager)
endif()

find_library(LIBRARY_EXISTS SettingsManager)
if(NOT LIBRARY_EXISTS)
add_subdirectory(Settings-Manager)
endif()
