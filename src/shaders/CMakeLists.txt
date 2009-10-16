macro (osl_compile oslsrc objlist headers)
    # message (STATUS "OSL_COMPILE src=${oslsrc}")
    # message (STATUS "  src ${CMAKE_CURRENT_SOURCE_DIR}")
    # message (STATUS "  bin ${CMAKE_CURRENT_BINARY_DIR}")
    get_filename_component ( oslsrc_we ${oslsrc} NAME_WE )
    set ( osofile "${CMAKE_CURRENT_BINARY_DIR}/${oslsrc_we}.oso" )
    # message (STATUS "  osl output ${osofile}")

    set ( ${objlist} ${${objlist}} ${osofile} )
    # message (STATUS "  obj list now ${${objlist}}")

#    include_directories ( ${CMAKE_CURRENT_BINARY_DIR} )
#    include_directories ( ${CMAKE_CURRENT_SOURCE_DIR} )
    message (STATUS "cmd: ${CMAKE_CURRENT_BINARY_DIR}/../oslc/oslc ${oslsrc}")
    add_custom_command (OUTPUT ${osofile}
        COMMAND ${CMAKE_CURRENT_BINARY_DIR}/../oslc/oslc ${oslsrc}
        MAIN_DEPENDENCY ${oslsrc}
        DEPENDS ${${headers}} ${CMAKE_CURRENT_BINARY_DIR}/../oslc/oslc
        WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR})
endmacro ()




file (GLOB shader_headers "*.h")
file (GLOB shader_source "*.osl")

foreach (_shadername ${shader_source})
    osl_compile (${_shadername} shader_objs shader_headers)
endforeach ()

add_custom_target (shaders ALL
                   DEPENDS ${shader_objs}
                   SOURCES ${shader_source} ${shader_headers})

install (FILES ${shader_headers} ${shader_source} ${shader_objs}
         DESTINATION shaders)
