cmake_minimum_required(VERSION 2.8)
project(bisonpp C)

set(exename "bison++")

###############
# ASI default settings
list(APPEND CMAKE_MODULE_PATH "$ENV{ASV_CMAKE}")
include(asv_cmake_defaults)
asv_cmake_defaults( )
###############

foreach(s
    lr0
    allocate.c
    closure.c
    conflict.c
    derives.c
    files.c
    getargs.c
    gram.c
    lalr.c lex.c
    main.c
    nullable.c
    output.c
    print.c
    reader.c
    reduce.c
    symtab.c
    version.c
    warshall.c
)
  list(APPEND bisonpp_sources src/${s})
endforeach()

add_executable(${exename})
target_sources(${exename} PRIVATE ${bisonpp_sources})
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
install(TARGETS ${exename} DESTINATION bin)

# copy support files
install(FILES src/bison.cc src/bison.h DESTINATION lib)

# copy docs
install(FILES doc/bison++.1 doc/bison++.1.dman DESTINATION man)
