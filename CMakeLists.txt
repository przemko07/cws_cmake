cmake_minimum_required(VERSION 3.20)
project(cws C)


# -----------------------
# Global compile settings
# -----------------------
set(CMAKE_C_STANDARD 11)
set(CMAKE_C_STANDARD_REQUIRED ON)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)


# -----------------------
# cMake options
# -----------------------
option(ENABLE_WERROR "Treat warnings as errors" OFF)
option(BUILD_EXAMPLES "Build C examples" ON)


# -----------------------
# Helper functions
# -----------------------
function(enable_strict_warnings target)
    if (MSVC)
        # W4        - displays level 1, level 2, and level 3 warnings, and all level 4 (informational) warnings
        # WX        - Treats all compiler warnings as errors.
        target_compile_options(${target} PRIVATE
            /W4
            $<$<BOOL:${ENABLE_WERROR}>:/WX>
        ) # target_compile_options
    else() # MSVC
        # Wall      - his enables all the warnings about constructions that some users consider questionable
        # Wextra    - This enables some extra warning flags that are not enabled by -Wall
        # Wpedantic - Issue all the warnings demanded by strict ISO C and ISO C++
        # Werror    - Turn all warnings into errors. 
        target_compile_options(${target} PRIVATE
            -Wall
            -Wextra
            -Wpedantic
            $<$<BOOL:${ENABLE_WERROR}>:-Werror>
        ) # target_compile_options
    endif() # MSVC
endfunction() # enable_strict_warnings


# -----------------------
# Library: libcws.a
# -----------------------
add_library(cws STATIC
    src/cws.c
    src/coroutine.c
) # add_library
enable_strict_warnings(cws)

target_include_directories(cws
    PUBLIC
        ${CMAKE_CURRENT_SOURCE_DIR}
        ${CMAKE_CURRENT_SOURCE_DIR}/src
) # target_include_directories


# -----------------------
# C Examples
# -----------------------
if (BUILD_EXAMPLES)
    add_executable(01_plain_echo_server
        examples/01_plain_echo_server.c
    )
    target_link_libraries(01_plain_echo_server cws)
    enable_strict_warnings(01_plain_echo_server)

    add_executable(02_plain_async_echo_server
        examples/02_plain_async_echo_server.c
    )
    target_link_libraries(02_plain_async_echo_server cws)
    enable_strict_warnings(02_plain_async_echo_server)


    # -----------------------
    # C3 example
    # -----------------------
    find_program(C3C_EXECUTABLE c3c)
    
    if (C3C_EXECUTABLE)
        add_custom_target(11_plain_echo_server_c3 ALL
            COMMAND ${C3C_EXECUTABLE} compile
                -g
                -l $<TARGET_FILE:cws>
                -o ${CMAKE_BINARY_DIR}/11_plain_echo_server
                ${CMAKE_CURRENT_SOURCE_DIR}/examples/11_plain_echo_server.c3
                ${CMAKE_CURRENT_SOURCE_DIR}/src/cws.c3
                ${CMAKE_CURRENT_SOURCE_DIR}/src/coroutine.c3
            DEPENDS cws
            WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
            COMMENT "Building C3 example (11_plain_echo_server)"
        )
    else() # C3C_EXECUTABLE
        message(STATUS "c3c not found, skipping C3 example")
    endif() # C3C_EXECUTABLE
endif() # BUILD_EXAMPLES
