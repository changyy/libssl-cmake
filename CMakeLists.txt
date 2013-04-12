cmake_minimum_required(VERSION 2.8)
project (libssl)

if (DEFINED CMAKE_TOOLCHAIN_FILE)
	message("Include Cross Platform cmake: " ${CROSS_PLATFORM_CMAKE})
	get_filename_component(COMPILER_NAME ${CMAKE_C_COMPILER} NAME)
	message( "CMAKE_C_COMPILER: ${COMPILER_NAME}")
	string(REGEX REPLACE "(.*)-gcc" "\\1" target_platform "${COMPILER_NAME}")
	message( "target_platform: ${target_platform}")
	set(target_platform_preconfigure_options "CC=${target_platform}-gcc;AR=${target_platform}-ar;LD=${target_platform}-ld;RANLIB=${target_platform}-ranlib;NM=${target_platform}-nm;AS=${target_platform}-as;OBJDUMP=${target_platform}-objdump;OBJCOPY=${target_platform}-objcopy;STRIP=${target_platform}-strip;STRINGS=${target_platform}-strings;SIZE=${target_platform}-size")
endif(DEFINED CMAKE_TOOLCHAIN_FILE)

set(TOOL_LIBSSL_SOURCE openssl-1.0.1e.tar.gz)
set(TOOL_LIBSSL_DIR openssl-1.0.1e)
set(CONFIG_ADD_OPTIONS "--prefix=${CMAKE_BINARY_DIR}")
	if(DEFINED CMAKE_TOOLCHAIN_FILE)
		add_custom_command(
			OUTPUT BUILD_LIBSSL
			COMMAND curl http://www.openssl.org/source/${TOOL_LIBSSL_SOURCE} | tar -xzvf - && cd ${CMAKE_BINARY_DIR}/${TOOL_LIBSSL_DIR} && ${target_platform_preconfigure_options} ./config os/complier:${target_platform}-gcc ${CONFIG_ADD_OPTIONS} && make && make install
			WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
			COMMENT "Build libssl"
		)
	else(DEFINED CMAKE_TOOLCHAIN_FILE)
		add_custom_command(
			OUTPUT BUILD_LIBSSL
			COMMAND curl http://www.openssl.org/source/${TOOL_LIBSSL_SOURCE} | tar -xzvf - && cd ${CMAKE_BINARY_DIR}/${TOOL_LIBSSL_DIR} && ./config ${CONFIG_ADD_OPTIONS} && make && make install
			WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
			COMMENT "Build libssl" 
		)
	endif(DEFINED CMAKE_TOOLCHAIN_FILE)
	add_custom_target(build_libssl ALL DEPENDS BUILD_LIBSSL )
