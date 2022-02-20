cmake_minimum_required(VERSION 3.0.0)
project(gameboy-emulator VERSION 0.1.0)

set (CMAKE_CXX_STANDARD 20)

cmake_policy(SET CMP0069 NEW)

set(EXECUTABLE_OUTPUT_PATH ${CMAKE_CURRENT_SOURCE_DIR}/bin)

set(SRC
    ${CMAKE_CURRENT_SOURCE_DIR}/src/main.cpp
)

add_executable(e ${SRC})

target_include_directories(e
    PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/include
    PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/lib
)

set(SDL2_INCLUDE_DIRS
    ${CMAKE_CURRENT_SOURCE_DIR}/lib/SDL2-devel-2.0.20-VC/SDL2-2.0.20/include
)
set(SDL2_LIBRARIES
    ${CMAKE_CURRENT_SOURCE_DIR}/lib/SDL2-devel-2.0.20-VC/SDL2-2.0.20/lib/x64/SDL2.lib
    ${CMAKE_CURRENT_SOURCE_DIR}/lib/SDL2-devel-2.0.20-VC/SDL2-2.0.20/lib/x64/SDL2main.lib
)

include_directories(${SDL2_INCLUDE_DIRS})
target_link_libraries(e ${SDL2_LIBRARIES})


if(${LTO})
    include(CheckIPOSupported)
    check_ipo_supported(RESULT result)
    if(result)
        set(CMAKE_INTERPROCEDURAL_OPTIMIZATION TRUE)
    endif()
endif()

if(MSVC)
    target_compile_options(e PRIVATE
        /W3
        # /Wall
        # # # C5045 Compiler will insert Spectre mitigation for memory load if /Qspectre switch specified
        # /wd5045
        # # # C5039 'function': pointer or reference to potentially throwing function passed to extern C function under -EHc. Undefined behavior may occur if this function throws an exception.
        # /wd5039
        # # # C4668 'symbol' is not defined as a preprocessor macro, replacing with '0' for 'directives'
        # /wd4668

        # /wd5027
        # /wd4626
        # /wd4820
        # /wd4061
        # /wd4062
        # /wd4623
    )
else()
    target_compile_options(e PRIVATE -Wall -Wextra -pedantic)
endif()