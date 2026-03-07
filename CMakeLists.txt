cmake_minimum_required(VERSION 3.15)
project(libudis86 C)

set(LIBUDIS86_SOURCES
    libudis86/decode.c
    libudis86/itab.c
    libudis86/syn-att.c
    libudis86/syn-intel.c
    libudis86/syn.c
    libudis86/udis86.c
)


set(LIBUDIS86_HEADERS
    libudis86/decode.h
    libudis86/extern.h
    libudis86/itab.h
    libudis86/syn.h
    libudis86/types.h
    libudis86/udint.h
)


add_library(libudis86 STATIC ${LIBUDIS86_SOURCES} ${LIBUDIS86_HEADERS})

target_compile_definitions(libudis86 PRIVATE
    WIN32
    _WINDOWS
    LIBUDIS86_EXPORTS
    $<$<CONFIG:Debug>:_DEBUG>
    $<$<NOT:$<CONFIG:Debug>>:NDEBUG>
)


if(MSVC)
    target_compile_options(libudis86 PRIVATE
        /W3
        $<$<CONFIG:Release>:/O2 /Oi /Ot /Gy> 
        $<$<CONFIG:Debug>:/Od>
    )

   
    target_link_options(libudis86 PRIVATE
        $<$<CONFIG:Release>:/O2 /Oi /Ot /Gy>
    )

    set_target_properties(libudis86 PROPERTIES
        COMPILE_PDB_NAME libudis86
    )
endif()


set(ARCH $<IF:$<EQUAL:${CMAKE_SIZEOF_VOID_P},8>,x64,x86>)

set_target_properties(libudis86 PROPERTIES
    ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_SOURCE_DIR}/bin/${ARCH}"
    LIBRARY_OUTPUT_DIRECTORY "${CMAKE_SOURCE_DIR}/bin/${ARCH}"
    RUNTIME_OUTPUT_DIRECTORY "${CMAKE_SOURCE_DIR}/bin/${ARCH}"

    ARCHIVE_OUTPUT_DIRECTORY_DEBUG   "${CMAKE_SOURCE_DIR}/bin/Debug/${ARCH}"
    LIBRARY_OUTPUT_DIRECTORY_DEBUG   "${CMAKE_SOURCE_DIR}/bin/Debug/${ARCH}"
    RUNTIME_OUTPUT_DIRECTORY_DEBUG   "${CMAKE_SOURCE_DIR}/bin/Debug/${ARCH}"

    ARCHIVE_OUTPUT_DIRECTORY_RELEASE "${CMAKE_SOURCE_DIR}/bin/Release/${ARCH}"
    LIBRARY_OUTPUT_DIRECTORY_RELEASE "${CMAKE_SOURCE_DIR}/bin/Release/${ARCH}"
    RUNTIME_OUTPUT_DIRECTORY_RELEASE "${CMAKE_SOURCE_DIR}/bin/Release/${ARCH}"
)

target_include_directories(libudis86 PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/libudis86>
    $<INSTALL_INTERFACE:include>
)