###############################################################################
## Name:        CMakeLists.txt
## Purpose:     To build wxOpenCVCameras application
## Author:      PB
## Created:     2021-11-18
## Copyright:   (c) 2021 PB
## Licence:     wxWindows licence
###############################################################################

cmake_minimum_required(VERSION 3.1 FATAL_ERROR)
project(wxOpenCVCameras)

set( CMAKE_MESSAGE_LOG_LEVEL "DEBUG" )
# you have to set variable under msys2, if not, the default wx-config is used and wx 3.0.x is used
set(wxWidgets_CONFIG_EXECUTABLE "wx-config-3.1")
find_package(wxWidgets 3.1.0 COMPONENTS core base REQUIRED)
find_package(OpenCV 4.2 REQUIRED)

set(SOURCES
  cameragridframe.h
  camerapanel.h
  camerathread.h
  convertmattowxbmp.h
  onecameraframe.h
  cameraapp.cpp
  cameragridframe.cpp
  camerapanel.cpp
  camerathread.cpp
  convertmattowxbmp.cpp
  onecameraframe.cpp
)

if (WIN32)
  list(APPEND SOURCES "${wxWidgets_ROOT_DIR}/include/wx/msw/wx.rc")
endif()

set_property(GLOBAL PROPERTY USE_FOLDERS ON)
set_property (DIRECTORY PROPERTY VS_STARTUP_PROJECT ${PROJECT_NAME})

add_executable(${PROJECT_NAME} ${SOURCES})

include(${wxWidgets_USE_FILE})

set_target_properties(${PROJECT_NAME} PROPERTIES
    CXX_STANDARD 11
    CXX_STANDARD_REQUIRED YES
)

# under msys2's wx, there is only a manifest file named:
# F:\msys2\mingw64\include\wx-3.1\wx\msw\amd64.manifest
# there is no file named: F:\msys2\mingw64\bin\windres.exe: can't open file `wx/msw/amd64_dpi_aware_pmv2.manifest': No such file or directory

if (WIN32)
  target_compile_definitions(${PROJECT_NAME} PRIVATE
    wxUSE_RC_MANIFEST
    wxUSE_DPI_AWARE_MANIFEST=0)
  set_target_properties(${PROJECT_NAME} PROPERTIES WIN32_EXECUTABLE YES)

  if (MSVC)
     set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} /MANIFEST:NO")
  endif(MSVC)

elseif(APPLE)
  set_target_properties(${PROJECT_NAME} PROPERTIES MACOSX_BUNDLE YES)
endif()

target_link_libraries(${PROJECT_NAME} PRIVATE ${wxWidgets_LIBRARIES} ${OpenCV_LIBS})