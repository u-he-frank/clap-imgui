cmake_minimum_required(VERSION 3.17)

project(clap-imgui C CXX)

add_definitions(-DCLAPPLUGIN_EXPORTS)

if(CMAKE_SYSTEM_NAME STREQUAL "Linux")

  add_definitions(-D_GLFW_X11)
  add_definitions(-DGLFW_EXPOSE_NATIVE_X11)

elseif(CMAKE_SYSTEM_NAME STREQUAL "Darwin")

  add_definitions(-D_GLFW_COCOA)
  add_definitions(-DGLFW_EXPOSE_NATIVE_COCOA)

elseif(CMAKE_SYSTEM_NAME STREQUAL "Windows")

  add_definitions(-D_GLFW_WIN32)
  add_definitions(-DGLFW_EXPOSE_NATIVE_WIN32)

endif()

add_subdirectory(libs EXCLUDE_FROM_ALL)

add_subdirectory(src)
