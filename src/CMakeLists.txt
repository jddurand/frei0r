include(CheckLinkerFlag)
include(FindPackageHandleStandardArgs)
include(CheckFunctionExists)
include(CheckCSourceCompiles)
include(CheckCXXSourceCompiles)
#
# Linker flag to avoid references to unneeded symbols
#
set(WL_AS_NEEDED "-Wl,--as-needed")
check_linker_flag(C ${WL_AS_NEEDED} LINKER_HAS_WL_AS_NEEDED)
if(LINKER_HAS_WL_AS_NEEDED)
  set(CMAKE_SHARED_LINKER_FLAGS ${WL_AS_NEEDED})
endif()
#
# Math library, e.g. -lm on UNIX, nothing on Windows
#
include(FindPackageHandleStandardArgs)
find_path(MATH_INCLUDE_DIR math.h)
set(MATH_NAMES ${MATH_NAMES} m)
find_library(MATH_LIBRARY NAMES ${MATH_NAMES})
find_package_handle_standard_args(MATH DEFAULT_MSG MATH_LIBRARY MATH_INCLUDE_DIR)
IF(MATH_FOUND)
  link_libraries(${MATH_LIBRARY})
ENDIF(MATH_FOUND)
#
# Look for working infinity
#
message(STATUS "Looking for working INFINITY")
try_compile(C_HAS_INFINITY ${CMAKE_CURRENT_BINARY_DIR} ${CMAKE_CURRENT_SOURCE_DIR}/infinity.c COMPILE_DEFINITIONS -DC_INFINITY=INFINITY)
if(C_HAS_INFINITY)
	message(STATUS "Looking for working INFINITY - found")
	set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DHAVE_C_INFINITY")
	set(CMAKE_CXX_FLAGS "${CMAKE_C_FLAGS} -DHAVE_C_INFINITY")
else()
	message(STATUS "Looking for INFINITY replacement")
	try_compile(C_HAS_INFINITY ${CMAKE_CURRENT_BINARY_DIR} ${CMAKE_CURRENT_SOURCE_DIR}/infinity.c COMPILE_DEFINITIONS -DHAVE_INFINITY_REPLACEMENT=1)
	if(C_HAS_INFINITY_REPLACEMENT)
		message(STATUS "Looking for INFINITY replacement - found")
		set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DHAVE_C_INFINITY_REPLACEMENT")
		set(CMAKE_CXX_FLAGS "${CMAKE_C_FLAGS} -DHAVE_C_INFINITY_REPLACEMENT")
	else()
		message(STATUS "Looking for INFINITY replacement using division")
		try_run(C_INFINITY_RUN_RESULT C_INFINITY_COMPILE_RESULT ${CMAKE_CURRENT_BINARY_DIR} ${CMAKE_CURRENT_SOURCE_DIR}/infinity.c COMPILE_DEFINITIONS -DHAVE_INFINITY_REPLACEMENT_USING_DIVISION=1)
		if(C_INFINITY_COMPILE_RESULT AND (C_INFINITY_RUN_RESULT EQUAL 0))
			message(STATUS "Looking for INFINITY replacement using division - found")
		else()
			message(WARNING "Using division by zero to represent infinity seems prone to failure")
		endif()
	endif()
endif()
#
# Look for roundf, rint, lrint
#
SET(CMAKE_EXTRA_INCLUDE_FILES "math.h")
check_function_exists(roundf HAVE_ROUNDF)
check_function_exists(rint HAVE_RINT)
check_function_exists(lrint HAVE_LRINT)
check_function_exists(lrintf HAVE_LRINTF)
SET(CMAKE_EXTRA_INCLUDE_FILES)
if(HAVE_ROUNDF)
	set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DHAVE_ROUNDF")
	set(CMAKE_CXX_FLAGS "${CMAKE_C_FLAGS} -DHAVE_ROUNDF")
endif()
if(HAVE_RINT)
	set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DHAVE_RINT")
	set(CMAKE_CXX_FLAGS "${CMAKE_C_FLAGS} -DHAVE_RINT")
endif()
if(HAVE_LRINT)
	set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DHAVE_LRINT")
	set(CMAKE_CXX_FLAGS "${CMAKE_C_FLAGS} -DHAVE_LRINT")
endif()
if(HAVE_LRINTF)
	set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DHAVE_LRINTF")
	set(CMAKE_CXX_FLAGS "${CMAKE_C_FLAGS} -DHAVE_LRINTF")
endif()
#
# Check that C/CXX compilers supports VLA
#
check_c_source_compiles("int main(void){for (int i = 1; i < 5; i++){int a[i];} return 0;}" C_SUPPORT_VLA)
check_cxx_source_compiles("int main(void){for (int i = 1; i < 5; i++){int a[i];} return 0;}" CXX_SUPPORT_VLA)
if (C_SUPPORT_VLA)
	set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DSUPPORT_VLA")
endif()
if (CXX_SUPPORT_VLA)
	set(CMAKE_CXX_FLAGS "${CMAKE_C_FLAGS} -DSUPPORT_VLA")
endif()
#
# Set defines specific to cl
#
if(MSVC)
	set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -D_CRT_SECURE_NO_WARNINGS")
	set(CMAKE_CXX_FLAGS "${CMAKE_C_FLAGS} -D_CRT_SECURE_NO_WARNINGS")
endif()

add_subdirectory (filter)
add_subdirectory (generator)
add_subdirectory (mixer2)
add_subdirectory (mixer3)
