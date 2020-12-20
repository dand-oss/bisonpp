cmake_minimum_required(VERSION 3.30)
project(bisonpp VERSION 1.0.0 LANGUAGES C)

set(exename "bison++")

###############
# ASI default settings
list(APPEND CMAKE_MODULE_PATH "$ENV{ASV_CMAKE}")
include(asv_cmake_defaults)
asv_cmake_defaults( )
###############

include(GNUInstallDirs)
include(CMakePackageConfigHelpers)

add_executable(${exename})
target_sources(${exename} PRIVATE
    src/lr0.c
    src/allocate.c
    src/closure.c
    src/conflict.c
    src/derives.c
    src/files.c
    src/getargs.c
    src/gram.c
    src/lalr.c
    src/lex.c
    src/main.c
    src/nullable.c
    src/output.c
    src/print.c
    src/reader.c
    src/reduce.c
    src/symtab.c
    src/version.c
    src/warshall.c
)
if(MSVC)
  # microsoft needs getopt
  target_sources(${exename} PRIVATE src/getopt.c src/getopt1.c)
endif()

target_compile_definitions(${exename} PRIVATE
    STDC_HEADERS=1
    HAVE_STRING_H=1
    HAVE_STDLIB_H=1
    HAVE_MEMORY_H=1
    HAVE_STRERROR=1
    STACK_DIRECTION=-1)
if ( WIN32 )
    target_compile_definitions(${exename} PRIVATE
        _MSDOS
        _CRT_SECURE_NO_WARNINGS
        )
endif()
if(UNIX)
    target_compile_definitions(${exename} PRIVATE
        HAVE_ALLOCA_H
        )
endif()

# copy exe
install(TARGETS ${exename}
    EXPORT bisonppTargets
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)

# copy support files
install(FILES src/bison.cc src/bison.h DESTINATION ${CMAKE_INSTALL_LIBDIR})

# copy docs
install(FILES doc/bison++.1 doc/bison++.1.dman DESTINATION man)

# Export all targets as a single bisonppTargets group
install(EXPORT bisonppTargets
    FILE bisonppTargets.cmake
    NAMESPACE bisonpp::
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/bisonpp
)

# Generate version file
write_basic_package_version_file(
    "${CMAKE_CURRENT_BINARY_DIR}/bisonppConfigVersion.cmake"
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY SameMajorVersion
)

# Configure and install bisonppConfig.cmake
configure_package_config_file(
    "${CMAKE_SOURCE_DIR}/bisonppConfig.cmake.in"
    "${CMAKE_CURRENT_BINARY_DIR}/bisonppConfig.cmake"
    INSTALL_DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/bisonpp
)

install(FILES
    "${CMAKE_CURRENT_BINARY_DIR}/bisonppConfig.cmake"
    "${CMAKE_CURRENT_BINARY_DIR}/bisonppConfigVersion.cmake"
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/bisonpp
)

# CPack configuration
set(CPACK_PACKAGE_NAME        "bisonpp")
set(CPACK_PACKAGE_VERSION     "${PROJECT_VERSION}")
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY
    "Bison++ parser generator"
)
set(CPACK_PACKAGE_VENDOR      "AppSmiths Ventures LLP")
set(CPACK_GENERATOR           "ZIP;TGZ")
include(CPack)