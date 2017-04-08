add_custom_target(data-files-module SOURCES org.valapanel.appmenu-gtk-module.gschema.xml appmenu-gtk-module.service)


include(GSettings)
add_schema("org.valapanel.appmenu-gtk-module.gschema.xml" "bin")
pkg_check_modules(SYSTEMD "systemd")
if (SYSTEMD_FOUND AND "${SYSTEMD_SERVICES_INSTALL_DIR}" STREQUAL "")
    execute_process(COMMAND ${PKG_CONFIG_EXECUTABLE}
        --variable=systemdsystemunitdir systemd
       OUTPUT_VARIABLE SYSTEMD_SERVICES_INSTALL_DIR)
    string(REGEX REPLACE "[ \t\n]+" "" SYSTEMD_SERVICES_INSTALL_DIR
        "${SYSTEMD_SERVICES_INSTALL_DIR}")
elseif (NOT SYSTEMD_FOUND AND SYSTEMD_SERVICES_INSTALL_DIR)
    message (FATAL_ERROR "Variable SYSTEMD_SERVICES_INSTALL_DIR is\
        defined, but we can't find systemd using pkg-config")
endif()
install(FILES appmenu-gtk-module.service DESTINATION ${SYSTEMD_SERVICES_INSTALL_DIR})
configure_file(appmenu-gtk2-parser.pc.in ${CMAKE_CURRENT_BINARY_DIR}/appmenu-gtk2-parser.pc)
configure_file(appmenu-gtk3-parser.pc.in ${CMAKE_CURRENT_BINARY_DIR}/appmenu-gtk3-parser.pc)
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/appmenu-gtk3-parser.pc DESTINATION ${CMAKE_INSTALL_LIBDIR}/pkgconfig)
if(GTK2_FOUND)
    install(FILES ${CMAKE_CURRENT_BINARY_DIR}/appmenu-gtk2-parser.pc DESTINATION ${CMAKE_INSTALL_LIBDIR}/pkgconfig)
endif()