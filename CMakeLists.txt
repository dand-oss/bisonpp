cmake_minimum_required(VERSION 3.1)
project(bisonpp VERSION 1.0.0 LANGUAGES C)

set(exename "bison++")

include(GNUInstallDirs)

add_executable(${exename})
target_sources(${exename} PRIVATE
    src/lr0
    src/allocate.c
    src/closure.c
    src/conflict.c
    src/derives.c
    src/files.c
    src/getargs.c
    src/gram.c
    src/lalr.c lex.c
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
install(TARGETS ${exename} DESTINATION bin)

# copy support files
install(FILES src/bison.cc src/bison.h DESTINATION lib)

# copy docs
install(FILES doc/bison++.1 doc/bison++.1.dman DESTINATION man)
