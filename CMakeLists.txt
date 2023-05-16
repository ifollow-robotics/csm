cmake_minimum_required(VERSION 3.12.0)
project(laser_scan_matcher)

## C++20 support
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_BUILD_TYPE None) #None, Debug, Release, ExtremeDebug
set(GCC_COVERAGE_COMPILE_FLAGS "-O3") # -O2 -O3 -march=native -w  |  "-Og -g -w"
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${GCC_COVERAGE_COMPILE_FLAGS}")


#cmake_minimum_required(VERSION 2.4)
#cmake_policy(SET CMP0004 OLD)
SET(local_prefix "local_config/local-")
FIND_PROGRAM(HOSTNAME hostname PATHS $ENV{PATH})
IF(HOSTNAME)
	# TODO: hostname does not accept -s on cygwin
	IF(CYGWIN OR WIN32)
		SET(hostname_params "")
	ELSE(CYGWIN OR WIN32)
		SET(hostname_params "-s")
	ENDIF(CYGWIN OR WIN32)
	
	EXEC_PROGRAM(${HOSTNAME} ARGS ${hostname_params} OUTPUT_VARIABLE host)

	SET(local "${local_prefix}${host}.cmake")
	
	SET(extension "$ENV{CSM_CONF}")
	IF(extension)
		SET(local "${local_prefix}${host}-${extension}.cmake")
	ENDIF(extension)
	
	IF(EXISTS ${local})
		MESSAGE(STATUS "Including local configuration ${local}.")
		INCLUDE(${local})
	ELSE(EXISTS ${local})
		MESSAGE(STATUS "Create a file named '${local}' if you want to add options.")
	ENDIF(EXISTS ${local})
ELSE(HOSTNAME)
	MESSAGE(STATUS "Program `hostname` not found. (don't worry)")
ENDIF(HOSTNAME)


ENABLE_TESTING()
SUBDIRS(sm)
