set(libName sokol)
cmake_minimum_required(VERSION 3.2)
project(${libName})
cmake_policy(SET CMP0079 NEW)

# include(ExternalProject)

# seti(EXTERNAL_INSTALL_LOCATION ${CMAKE_BINARY_DIR}/external)

# ExternalProject_Add(sokolrepo
#     GIT_REPOSITORY https://github.com/floooh/sokol
#     CMAKE_ARGS -DCMAKE_INSTALL_PREFIX=${EXTERNAL_INSTALL_LOCATION}
# )

include(FetchContent)

FetchContent_Declare(
				sokolrepo
				URL https://github.com/floooh/sokol/archive/master.tar.gz
)

FetchContent_GetProperties(sokolrepo)
if (NOT sokolrepo_POPULATED)    
    set(FETCHCONTENT_QUIET NO)
    FetchContent_Populate(sokolrepo)
endif()


# set(repoDir ${EXTERNAL_INSTALL_LOCATION})
set(repoDir ${sokolrepo_SOURCE_DIR})

# include_directories(${EXTERNAL_INSTALL_LOCATION}/include)
# target_link_directories(sokol ${EXTERNAL_INSTALL_LOCATION}/lib)




#=== LIBRARY: sokol
# add headers to the the file list because they are useful to have in IDEs
set(SOKOL_HEADERS
    ${repoDir}/sokol_gfx.h
    ${repoDir}/sokol_app.h
    ${repoDir}/sokol_audio.h
    ${repoDir}/sokol_time.h
    ${repoDir}/sokol_glue.h
		./sokol.c
)

if (NOT TARGET sokol)
    # add_subdirectory("${CMAKE_SOURCE_DIR}/common/libplugin")
    add_library(sokol STATIC ./sokol.c ${SOKOL_HEADERS})
endif()



# target_sources(sokol PUBLIC ${SOKOL_HEADERS})# add_dependencies(sokol sokolrepo)
if(CMAKE_SYSTEM_NAME STREQUAL Darwin)
    # compile sokol.c as Objective-C
    target_compile_options(sokol PRIVATE -x objective-c)
    target_link_libraries(sokol
        "-framework QuartzCore"
        "-framework Cocoa"
        "-framework MetalKit"
        "-framework Metal"
        "-framework AudioToolbox")
else()
    if (CMAKE_SYSTEM_NAME STREQUAL Linux)
        target_link_libraries(sokol INTERFACE X11 Xi Xcursor GL asound dl m)
        target_link_libraries(sokol PUBLIC Threads::Threads)
    endif()
endif()
# target_include_directories(sokol INTERFACE ../sokol)
# target_include_directories(sokol INTERFACE ${EXTERNAL_INSTALL_LOCATION})
target_include_directories(sokol PUBLIC ${repoDir} ${repoDir}/util)
# target_include_directories(sokol INTERFACE ${repoDir})









# set_target_properties(${libName} PROPERTIES LINKER_LANGUAGE CXX)
set_target_properties(${libName} PROPERTIES LINKER_LANGUAGE C)
# target_compile_options(${libName} PRIVATE -Wno-unused-variable -Wno-unused-value)
# target_include_directories(${libName} INTERFACE .)
