#include_directories(/usr/local/libiodbc-3.52.12/include)

set(VERSION ${CMAKE_CURRENT_BINARY_DIR}${CMAKE_FILES_DIRECTORY}/version.o)

add_definitions(-DNLP_EXPORTS)
if(WIN32)
    execute_process(COMMAND cmd /c ${CMAKE_SOURCE_DIR}/nlp/makeversion.bat > ${CMAKE_SOURCE_DIR}/nlp/version.cpp)
    add_definitions(-DMSVC_VERSION=${MSVC_VERSION} -D_CRT_SECURE_NO_WARNINGS)
else()
    add_definitions(-DLINUX -Wno-write-strings -Wno-deprecated)
endif()

file(GLOB nlp_src "*.h" "*.cpp")

if(NOT DEFINED CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE "Release")
endif()

if(NOT WIN32)
    find_library(DL_LIBRARY dl)
endif()

add_executable(nlp ${nlp_src})

if(WIN32)
    target_link_libraries(nlp PUBLIC prim kbm consh words lite ${ICU_LIBRARIES})
else()
    target_link_libraries(nlp PUBLIC prim kbm consh words lite ${ICU_LIBRARIES} ${DL_LIBRARY})
    set_target_properties(nlp PROPERTIES INSTALL_RPATH "$ORIGIN/icu-libs")
endif()
