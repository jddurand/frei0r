set (SOURCES defish0r.c interp.h)
set (TARGET defish0r)

if (MSVC)
  set (SOURCES ${SOURCES} ${FREI0R_DEF})
endif (MSVC)

add_library (${TARGET}  MODULE ${SOURCES})
if(MATH_LIBRARY)
	target_link_libraries(${TARGET} ${MATH_LIBRARY})
endif()
set_target_properties (${TARGET} PROPERTIES PREFIX "")

install (TARGETS ${TARGET} LIBRARY DESTINATION ${LIBDIR})
