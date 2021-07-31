cmake_minimum_required(VERSION 3.11)
# The FetchModule is only available since CMake 3.11.

include(FetchContent)

find_library(LIBRARY_EXISTS ModuleManager)
if(NOT LIBRARY_EXISTS)
FetchContent_Declare(
    Module-Manager
    GIT_REPOSITORY "https://github.com/bradosia/Module-Manager.git"
    SOURCE_DIR "${PROJECT_SOURCE_DIR}/third_party/Module-Manager"
)
FetchContent_Populate(Module-Manager)
add_subdirectory(Module-Manager)
endif()

find_library(LIBRARY_EXISTS SettingsManager)
if(NOT LIBRARY_EXISTS)
FetchContent_Declare(
    Settings-Manager
    GIT_REPOSITORY "https://github.com/bradosia/Settings-Manager.git"
    SOURCE_DIR "${PROJECT_SOURCE_DIR}/third_party/Settings-Manager"
)
FetchContent_Populate(Settings-Manager)
add_subdirectory(Settings-Manager)
endif()
