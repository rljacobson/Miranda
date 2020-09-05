cmake_minimum_required(VERSION 3.17)
project(miranda)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_C_STANDARD 11)

# Install paths relative to root.
# For linux, MacOS X, Cygwin:
set(BIN usr/bin)
set(LIB usr/lib)
set(MAN usr/share/man/man1)
#for Solaris:
#BIN=usr/local/bin
#LIB=usr/local/lib#beware no spaces after LIB
#MAN=usr/local/man/man1

#EX = #.exe        #needed for CYGWIN, UWIN

# Berkeley yacc, gnu yacc not compatible
set(YACC byacc)
# todo: Generate y.tab.c and y.tab.h
#y.tab.c y.tab.h: rules.y
#   $(YACC) -d rules.y


include_directories(src)

# To increment the "version" on every build, use:
#       MATH(EXPR MIRA_VERSION "${MIRA_VERSION}+1")
# Note that MIRA_VERSION IS DIFFERENT FROM xversion.h. The later is the bytecode file format.
set(MIRA_VERSION 2066)
cmake_host_system_information(RESULT MIRA_HOST_INFO QUERY OS_PLATFORM)
set(MIRA_HOST_INFO  "${MIRA_HOST_INFO} ${CMAKE_SYSTEM_NAME} ${CMAKE_SYSTEM_VERSION}")

set(MIRA_DEFINES
    -DVERS=${MIRA_VERSION}
    -DHOST="${MIRA_HOST_INFO}"

)
# The ${VDATE} is the latest revision date, which is the latest modified date of the sources.
#        -DVDATE="\"`./revdate`\""

set(HEADER
    src/big.h
    src/combs.h
    src/data.h
    src/lex.h
    src/utf8.h
    src/xversion.h
    src/y.tab.h
    )

set(SOURCES
    ${HEADERS}
    src/big.c
    src/cmbnms.c
    src/data.c
    src/lex.c
    src/reduce.c
    src/steer.c
    src/trans.c
    src/types.c
    src/utf8.c
    src/version.c
    src/y.tab.c
    )

add_executable(mira ${SOURCES})
target_link_libraries(mira m)

# Be wary of using anything higher than -O as the garbage collector may fall over
# If using gcc rather than clang try without -O first
target_compile_options(mira PRIVATE -O ${MIRA_DEFINES})
# Other possibly required flags:
#    -DCYGWIN #-DUWIN #-DIBMRISC #-Dsparc7 #-Dsparc8
# Needed for Solaris 2.7: -Dsparc7
# Needed for Solaris 2.8 or later: -Dsparc8


add_executable(menudriver src/menudriver.c)
# Alternative: use shell script
#	ln -s miralib/menudriver.sh miralib/menudriver
