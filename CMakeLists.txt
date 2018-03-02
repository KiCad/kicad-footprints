project( kicad-footprints NONE )

cmake_minimum_required( VERSION 2.6.1 FATAL_ERROR )


#================================================
# Locations for install targets.
#================================================
if( APPLE )
    # Like all variables, CMAKE_INSTALL_PREFIX can be over-ridden on the command line.
    set( CMAKE_INSTALL_PREFIX "/Library/Application Support/kicad/" CACHE PATH "" )
    # Everything without leading / is relative to CMAKE_INSTALL_PREFIX.
    set( KICAD_MODULES modules )
    set( KICAD_TEMPLATE template )
else()
    # Everything without leading / is relative to CMAKE_INSTALL_PREFIX.
    set( KICAD_DATA share/kicad
        CACHE PATH "Location of KiCad data files." )
    set( KICAD_MODULES ${KICAD_DATA}/modules )
    set( KICAD_TEMPLATE ${KICAD_DATA}/template )
endif()

mark_as_advanced( KICAD_DATA KICAD_MODULES )


#================================================
# "make uninstall" rules
#
# The uninstaller does not remove folders only files.
#================================================
configure_file(
    "${PROJECT_SOURCE_DIR}/CMakeModules/cmake_uninstall.cmake.in"
    "${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake"
    IMMEDIATE @ONLY
)

add_custom_target( uninstall
    "${CMAKE_COMMAND}" -P "${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake"
)


#================================================
# Installed files.
#================================================
# select pretty directories
file (GLOB PRETTY_DIRS "*.pretty")

# Install the contents of all of the .pretty folders in to the modules path.
install( DIRECTORY ${PRETTY_DIRS}
    DESTINATION ${KICAD_MODULES}
    COMPONENT resources
    FILES_MATCHING PATTERN "*.kicad_mod"
    PATTERN ".git" EXCLUDE
)

# Install the default global footprint library table in the template folder.
install( FILES fp-lib-table
    DESTINATION ${KICAD_TEMPLATE}
    COMPONENT resources
)
