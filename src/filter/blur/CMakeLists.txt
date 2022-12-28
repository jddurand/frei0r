set (SOURCES IIRblur.c fibe.h)
set (TARGET IIRblur)

add_library (${TARGET}  MODULE ${SOURCES})
if(MATH_LIBRARY)
	target_link_libraries(${TARGET} ${MATH_LIBRARY})
endif()
set_target_properties (${TARGET} PROPERTIES PREFIX "")

install (TARGETS ${TARGET} LIBRARY DESTINATION ${LIBDIR})
