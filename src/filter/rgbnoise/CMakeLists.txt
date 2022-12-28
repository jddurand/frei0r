set (SOURCES rgbnoise.c)
set (TARGET rgbnoise)

if (MSVC)
  set (SOURCES ${SOURCES} ${FREI0R_DEF})
endif (MSVC)

add_library (${TARGET}  MODULE ${SOURCES})
if(MATH_FOUND)
	target_link_libraries(${TARGET} ${MATH_LIBRARY})
endif()
set_target_properties (${TARGET} PROPERTIES PREFIX "")

install (TARGETS ${TARGET} LIBRARY DESTINATION ${LIBDIR})
