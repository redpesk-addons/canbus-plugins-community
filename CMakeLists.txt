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
	VERSION 1.1.0
	DESCRIPTION "Plugins for the CAN low level Binding"
	LANGUAGES C CXX)

include(FindPkgConfig)
include(GNUInstallDirs)
include(CTest)
enable_testing()

set(CAN_LOW_LEVEL_BINDING_NAME "canbus-binding" CACHE STRING "Name of the CAN low level binding. Needed to install the config and plugin at the correct place")
option(USE_LEGACY_CONTROLLER "Generate plugins using legacy controller" NO)

# Compilation Mode (DEBUG, RELEASE)
# ----------------------------------
set(CMAKE_BUILD_TYPE "RELEASE" CACHE STRING "Default Build variant chosen. (Overwritten by cli if given)")

# Prefix path where will be installed the files
# Default: /usr/local (need root permission to write in)
# ------------------------------------------------------
set(INSTALL_PREFIX $ENV{HOME}/opt)

# Customize link option
# -----------------------------
list (APPEND link_libraries -pthread)

add_subdirectory("etc")

find_program(cbpgen can-config-generator REQUIRED)
if(USE_LEGACY_CONTROLLER)
	set(mods canbus-binding afb-libhelpers afb-libcontroller)
	set(cbpgenopt -3)
else(USE_LEGACY_CONTROLLER)
	set(mods canbus-binding>=2)
	set(cbpgenopt -4)
endif(USE_LEGACY_CONTROLLER)
pkg_check_modules(modules REQUIRED ${mods})

add_compile_options(${modules_CFLAGS})
add_link_options(${modules_LDFLAGS})
link_libraries(${modules_LINK_LIBRARIES})

set(TARGETS_LIST "" CACHE INTERNAL "Enumerate targets")

function(add_canbus_plugin NAME)

	# Define targets
	add_library(${NAME} SHARED
		${CMAKE_CURRENT_BINARY_DIR}/${NAME}.cpp)

	# Create dedicated generate targets
	add_custom_command(COMMAND ${cbpgen} ${cbpgenopt}
				-m ${CMAKE_CURRENT_SOURCE_DIR}/${NAME}.json
				-o ${CMAKE_CURRENT_BINARY_DIR}/${NAME}.cpp
			OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/${NAME}.cpp)
	add_custom_target(generate_${NAME}_source
			DEPENDS ${CMAKE_CURRENT_BINARY_DIR}/${NAME}.cpp)

	# Plugin properties
	SET_TARGET_PROPERTIES(${NAME} PROPERTIES
		PREFIX ""
		OUTPUT_NAME ${NAME}
	)
	if(USE_LEGACY_CONTROLLER)
		SET_TARGET_PROPERTIES(${NAME} PROPERTIES
			SUFFIX ".ctlso"
		)
	endif(USE_LEGACY_CONTROLLER)

	set(TARGETS_LIST "${TARGETS_LIST};${NAME}" CACHE INTERNAL "Enumerate targets" FORCE)
endfunction(add_canbus_plugin)




file(GLOB plugin_dirs LIST_DIRECTORIES true src/*)

foreach(dir ${plugin_dirs})
	add_subdirectory(${dir} EXCLUDE_FROM_ALL)
endforeach()

foreach(target ${TARGETS_LIST})
	target_compile_definitions(${target} PRIVATE AFB_BINDING_VERSION=3)
	get_target_property(pre ${target} PREFIX)
	get_target_property(out ${target} OUTPUT_NAME)
	get_target_property(suf ${target} SUFFIX)
	get_target_property(bdir ${target} BINARY_DIR)
	message("=== Including plugin: '${target}'")
	# Create the dedicated install targets
	add_custom_target(install_${target}
		COMMAND install -d \${DESTDIR}${CMAKE_INSTALL_PREFIX}/${CAN_LOW_LEVEL_BINDING_NAME}/lib/plugins
		COMMAND install ${bdir}/${pre}${out}${suf} \${DESTDIR}${CMAKE_INSTALL_PREFIX}/${CAN_LOW_LEVEL_BINDING_NAME}/lib/plugins
		COMMAND
	)
	add_dependencies(install_${target} ${target})
endforeach()



