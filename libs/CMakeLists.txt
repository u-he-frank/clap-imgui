cmake_minimum_required(VERSION 3.17)

##################################################################################################################
# CLAP
##################################################################################################################

add_definitions(-DCLAPPLUGIN_EXPORTS)

add_subdirectory(clap EXCLUDE_FROM_ALL)

##################################################################################################################
# glfw
##################################################################################################################

option(GLFW_BUILD_EXAMPLES "Build the GLFW example programs" OFF)
option(GLFW_BUILD_TESTS "Build the GLFW test programs" OFF)
option(GLFW_BUILD_DOCS "Build the GLFW documentation" OFF)
option(GLFW_INSTALL "Generate installation target" OFF)

add_subdirectory(glfw EXCLUDE_FROM_ALL)

##################################################################################################################
# imgui
##################################################################################################################

project(IMGUI C CXX)

set(SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/imgui)

set(IMGUI_HEADERS_CXX_FILES 
    ${SOURCE_DIR}/imgui.h
    ${SOURCE_DIR}/imconfig.h
    ${SOURCE_DIR}/imgui_internal.h
    ${SOURCE_DIR}/imstb_rectpack.h
    ${SOURCE_DIR}/imstb_textedit.h
    ${SOURCE_DIR}/imstb_truetype.h
)

set(IMGUI_SOURCES_CXX_FILES 
    ${SOURCE_DIR}/imgui.cpp
    ${SOURCE_DIR}/imgui_draw.cpp
    ${SOURCE_DIR}/imgui_knob.cpp    
    ${SOURCE_DIR}/imgui_tables.cpp
    ${SOURCE_DIR}/imgui_widgets.cpp
)

set(BACKEND_DIR ${SOURCE_DIR}/backends)

set(IMGUI_HEADERS_CXX_BACKEND_FILES 
    ${BACKEND_DIR}/imgui_impl_glfw.h
    ${BACKEND_DIR}/imgui_impl_opengl3.h
)

set(IMGUI_SOURCES_CXX_BACKEND_FILES 
    ${BACKEND_DIR}/imgui_impl_glfw.cpp
    ${BACKEND_DIR}/imgui_impl_opengl3.cpp
)

if(CMAKE_SYSTEM_NAME STREQUAL "Linux")

  list (APPEND IMGUI_HEADERS_CXX_BACKEND_FILES  ${BACKEND_DIR}/imgui_impl_osx.h)
  list (APPEND IMGUI_SOURCES_CXX_BACKEND_FILES  ${BACKEND_DIR}/imgui_impl_osx.mm)
 
elseif(CMAKE_SYSTEM_NAME STREQUAL "Darwin")
  
  list (APPEND IMGUI_HEADERS_CXX_BACKEND_FILES  ${BACKEND_DIR}/imgui_impl_glfw.h)
  list (APPEND IMGUI_HEADERS_CXX_BACKEND_FILES  ${BACKEND_DIR}/imgui_impl_glfw.cpp)
  list (APPEND IMGUI_SOURCES_CXX_BACKEND_FILES  ${BACKEND_DIR}/imgui_impl_opengl3.h)
  list (APPEND IMGUI_SOURCES_CXX_BACKEND_FILES  ${BACKEND_DIR}/imgui_impl_opengl3_loader.h)
  list (APPEND IMGUI_SOURCES_CXX_BACKEND_FILES  ${BACKEND_DIR}/imgui_impl_opengl3.cpp)
            
elseif(CMAKE_SYSTEM_NAME STREQUAL "Windows")

  list (APPEND IMGUI_HEADERS_CXX_BACKEND_FILES  ${BACKEND_DIR}/imgui_impl_win32.h)
  list (APPEND IMGUI_SOURCES_CXX_BACKEND_FILES  ${BACKEND_DIR}/imgui_impl_win32.cpp)

endif()

file(GLOB IMGUI_FONTS_FILES ${FONTS_DIR}/*.ttf)
  
add_library(IMGUI 
    ${IMGUI_HEADERS_CXX_FILES}
    ${IMGUI_SOURCES_CXX_FILES}
    ${IMGUI_HEADERS_CXX_BACKEND_FILES}
    ${IMGUI_SOURCES_CXX_BACKEND_FILES}
    ${IMGUI_FONTS_FILES}
)

set_target_properties(IMGUI PROPERTIES CXX_STANDARD 14)

target_include_directories(IMGUI PRIVATE
     ${SOURCE_DIR}
     ${BACKEND_DIR}
     ${CMAKE_SOURCE_DIR}/libs/glfw/include
  )

##################################################################################################################
# IDE
##################################################################################################################

source_group("Source Files" FILES 
	${IMGUI_SOURCES_CXX_FILES}
)

source_group("Header Files" FILES 
	${IMGUI_HEADERS_CXX_FILES} 
)

source_group("Backend\\Source Files" FILES 
	${IMGUI_SOURCES_CXX_BACKEND_FILES}
)

source_group("Backend\\Header Files" FILES 
	${IMGUI_HEADERS_CXX_BACKEND_FILES} 
)