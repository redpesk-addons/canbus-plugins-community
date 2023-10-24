###########################################################################
# Copyright 2015 - 2018 IoT.bzh
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
###########################################################################

CMAKE_MINIMUM_REQUIRED(VERSION 3.5)

# Project Info
# ------------------
project(canbus-plugins-community
	VERSION 2.0.1
	DESCRIPTION "Plugins for the CAN low level Binding"
	LANGUAGES C CXX)

include(FindPkgConfig)
include(GNUInstallDirs)
include(CTest)
enable_testing()

set(CAN_LOW_LEVEL_BINDING_NAME "canbus-binding" CACHE STRING "Name of the CAN low level binding. Needed to install the config and plugin at the correct place")
set(AFM_APP_DIR ${CMAKE_INSTALL_PREFIX}/redpesk CACHE PATH "Application directory of redpesk")
option(USE_LEGACY_CONTROLLER "Generate plugins using legacy controller" NO)
option(MAKEALL "Make and install all" YES)

find_program(cbpgen can-config-generator REQUIRED)
if(NOT cbpgen)
	message(FATAL_ERROR "Can not locate can-config-generator")
endif(NOT cbpgen)

if(USE_LEGACY_CONTROLLER)
	set(mods canbus-binding afb-libhelpers afb-libcontroller)
	set(cbpgenopt -3)
	set(afbver 3)
	set(ext .ctlso)
else(USE_LEGACY_CONTROLLER)
	set(mods canbus-binding>=2)
	set(cbpgenopt -4)
	set(afbver 4)
	set(ext .so)
endif(USE_LEGACY_CONTROLLER)
if(NOT MAKEALL)
	set(exclude EXCLUDE_FROM_ALL)
endif(NOT MAKEALL)


pkg_check_modules(modules REQUIRED ${mods})
add_compile_options(${modules_CFLAGS})
add_link_options(${modules_LDFLAGS})
link_libraries(${modules_LINK_LIBRARIES})

function(add_canbus_plugin NAME)

	message("=== Including plugin: '${NAME}'")

	# Create dedicated generate targets
	add_custom_command(COMMAND ${cbpgen} ${cbpgenopt}
				-m ${CMAKE_CURRENT_SOURCE_DIR}/${NAME}.json
				-o ${CMAKE_CURRENT_BINARY_DIR}/${NAME}.cpp
			OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/${NAME}.cpp)

	# Define targets
	add_library(${NAME} SHARED ${exclude} ${CMAKE_CURRENT_BINARY_DIR}/${NAME}.cpp)
	target_compile_definitions(${NAME} PRIVATE AFB_BINDING_VERSION=${afbver})
	set_target_properties(${NAME} PROPERTIES PREFIX plugin- SUFFIX ${ext})

	# Install as redpesk component
	configure_file(
		${CMAKE_SOURCE_DIR}/manifest.yml.in
		${CMAKE_CURRENT_BINARY_DIR}/manifest.yml
		@ONLY)

        install(TARGETS ${NAME} DESTINATION ${AFM_APP_DIR}/${NAME}/lib
	        COMPONENT ${NAME} ${exclude})
        install(FILES ${CMAKE_CURRENT_BINARY_DIR}/manifest.yml DESTINATION ${AFM_APP_DIR}/${NAME}/.rpconfig
	        COMPONENT ${NAME} ${exclude})

endfunction(add_canbus_plugin)

file(GLOB plugin_dirs LIST_DIRECTORIES true src/*)
foreach(dir ${plugin_dirs})
	add_subdirectory(${dir})
endforeach()

if(NOT MAKEALL)
	message("")
	message(" for installing plugin X type: make X && cmake -DCOMPONENT=X -P cmake_install.cmake")
	message("")
endif(NOT MAKEALL)
