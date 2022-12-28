set (SOURCES coloradj_RGB.c)
set (TARGET coloradj_RGB)

if (MSVC)
  set (SOURCES ${SOURCES} ${FREI0R_DEF})
endif (MSVC)

add_library (${TARGET} MODULE ${SOURCES})
if(MATH_LIBRARY)
	target_link_libraries(${TARGET} ${MATH_LIBRARY})
endif()
set_target_properties (${TARGET} PROPERTIES PREFIX "")

install (TARGETS ${TARGET} LIBRARY DESTINATION ${LIBDIR})
