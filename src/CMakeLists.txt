cmake_minimum_required(VERSION 3.17)
cmake_policy(SET CMP0091 NEW) # use CMAKE_MSVC_RUNTIME_LIBRARY
cmake_policy(SET CMP0100 NEW) # handle .hh files
cmake_policy(SET CMP0110 NEW) # arbitrary test name

set(CMAKE_MSVC_RUNTIME_LIBRARY MultiThreadedDLL)

##################################################################################################################
# Setup 
##################################################################################################################

if(${CMAKE_CXX_COMPILER_ID} MATCHES "AppleClang|Clang|GNU")
  add_compile_options(-fvisibility=hidden -fvisibility-inlines-hidden
    -Werror=return-type -Werror=unused-result -Wno-unused-parameter
    -Wuninitialized -Wvla -Wmultichar -Werror=non-virtual-dtor -Woverloaded-virtual)
endif()

if(${CMAKE_CXX_COMPILER_ID} MATCHES "AppleClang|Clang")
  add_compile_options(-Wmost -Wsuper-class-method-mismatch)
endif()

##################################################################################################################
# Project Files
##################################################################################################################

set(clap-imgui-src

	gui.cpp
	imgui_base.cpp
	main.cpp
	main.h
	plugin.cpp
	plugin_impl_0.cpp
	plugin_impl_1.cpp
)

if(CMAKE_SYSTEM_NAME STREQUAL "Linux")

  list (APPEND clap-imgui-src imgui_lin.cpp)
  
elseif(CMAKE_SYSTEM_NAME STREQUAL "Darwin")
  
  list (APPEND clap-imgui-src imgui_mac.mm)
            
elseif(CMAKE_SYSTEM_NAME STREQUAL "Windows")

  list (APPEND clap-imgui-src imgui_win.cpp)

endif()

##################################################################################################################
# Target
##################################################################################################################

add_library(clap-imgui MODULE ${clap-imgui-src})

set_target_properties(clap-imgui PROPERTIES CXX_STANDARD 17)

target_compile_definitions(clap-imgui PRIVATE _USE_MATH_DEFINES)
target_compile_definitions(clap-imgui PUBLIC CLAP_LOCAL_GUI)

target_include_directories(clap-imgui PRIVATE 
     ${CMAKE_SOURCE_DIR}/libs/clap/include
     ${CMAKE_SOURCE_DIR}/libs/imgui
     ${CMAKE_SOURCE_DIR}/libs/glfw/include
)

add_dependencies(clap-imgui IMGUI glfw)

target_link_libraries(clap-imgui PRIVATE IMGUI glfw)

if(CMAKE_SYSTEM_NAME STREQUAL "Linux")

  target_link_libraries(clap-imgui PRIVATE -Wl,--version-script=${CMAKE_CURRENT_SOURCE_DIR}/linux/linux-clap-plugins.version)
  target_link_libraries(clap-imgui PRIVATE -Wl,-z,defs)
  set_target_properties(clap-imgui PROPERTIES SUFFIX ".clap" PREFIX "")
  
  target_compile_definitions(clap-imgui PRIVATE -DGLFW_EXPOSE_NATIVE_X11)

elseif(CMAKE_SYSTEM_NAME STREQUAL "Darwin")

  target_link_options(clap-imgui PRIVATE -exported_symbols_list ${CMAKE_CURRENT_SOURCE_DIR}/mac/macos-symbols.txt)
 
  find_library(GAME_CONTROLLER GameController) 
  target_link_libraries(clap-imgui PRIVATE ${GAME_CONTROLLER})
  
  set_target_properties(clap-imgui PROPERTIES
            BUNDLE True
            BUNDLE_EXTENSION clap
            MACOSX_BUNDLE_GUI_IDENTIFIER com.cockos.clap-imgui
            MACOSX_BUNDLE_BUNDLE_NAME clap-imgui
            MACOSX_BUNDLE_BUNDLE_VERSION "1"
            MACOSX_BUNDLE_SHORT_VERSION_STRING "1"
            MACOSX_BUNDLE_INFO_PLIST ${CMAKE_CURRENT_SOURCE_DIR}/mac/Info.plist.in
            )
  
  target_compile_definitions(clap-imgui PRIVATE -DGLFW_EXPOSE_NATIVE_COCOA)
            
elseif(CMAKE_SYSTEM_NAME STREQUAL "Windows")

  set_target_properties(clap-imgui PROPERTIES SUFFIX ".clap" PREFIX "")

  target_compile_definitions(clap-imgui PRIVATE -DGLFW_EXPOSE_NATIVE_WIN32)

endif()

##################################################################################################################
# Install
##################################################################################################################

install(TARGETS clap-imgui DESTINATION "lib/clap" COMPONENT plugins)
