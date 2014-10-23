cmake_minimum_required(VERSION 2.8)
project(bisonpp C)

if ( MSVC )
    # stop whining
    add_definitions(-D_CRT_SECURE_NO_WARNINGS -D__NT__)
endif()

# used by all files
add_definitions(-DSTDC_HEADERS=1 -DHAVE_STRING_H=1 -DHAVE_STDLIB_H=1 -DHAVE_MEMORY_H=1 -DHAVE_STRERROR=1 -DSTACK_DIRECTION=-1)

if ( WIN32 )
    # seems to help
    add_definitions(-D_MSDOS)
    # needed for getopt
    add_definitions(-DSTATIC_GETOPT)
endif()

foreach(s
    lr0 allocate.c closure.c conflict.c derives.c files.c
    getargs.c gram.c lalr.c lex.c
    main.c nullable.c output.c print.c reader.c reduce.c symtab.c
    version.c warshall.c
)
  list(APPEND bisonpp_sources src/${s})
endforeach()

if(WIN32)
  # windows needs getopt
  list(APPEND bisonpp_sources src/getopt.c)
endif()

add_executable(bison++ ${bisonpp_sources})

# copy exe
install(TARGETS bison++ DESTINATION bin)
# copy support files
install(FILES src/bison.cc src/bison.h DESTINATION lib)
# copy docs
install(FILES doc/bison++.1 doc/bison++.1.dman DESTINATION man)
