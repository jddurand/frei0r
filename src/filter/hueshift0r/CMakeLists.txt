set (SOURCES hueshift0r.c matrix.h)
set (TARGET hueshift0r)

if (MSVC)
  set (SOURCES ${SOURCES} ${FREI0R_DEF})
endif (MSVC)

add_library (${TARGET}  MODULE ${SOURCES})
set_target_properties (${TARGET} PROPERTIES PREFIX "")
if(MATH_FOUND)
	target_link_libraries(${TARGET} ${MATH_LIBRARY})
endif()

install (TARGETS ${TARGET} LIBRARY DESTINATION ${LIBDIR})
