cmake_minimum_required (VERSION 2.6)
project (tlmDecoder)
enable_language(CXX)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake/Modules/")
include(GetGitRevisionDescription)
get_git_head_revision(GIT_REFSPEC GIT_SHA1)

# C++11 Macro
macro(use_cxx11)
  if (CMAKE_VERSION VERSION_LESS "3.1")
    if (CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
      set (CMAKE_CXX_FLAGS "--std=gnu++11 ${CMAKE_CXX_FLAGS}")
    endif ()
  else ()
    set (CMAKE_CXX_STANDARD 11)
  endif ()
endmacro(use_cxx11)

use_cxx11()

# Version
set(VERSION_INFO_MAJOR_VERSION 1)
set(VERSION_INFO_MINOR_VERSION 0)
set(VERSION_INFO_MAINT_VERSION 1git)

add_definitions(-DMAJOR_VERSION=${VERSION_INFO_MAJOR_VERSION})
add_definitions(-DMINOR_VERSION=${VERSION_INFO_MINOR_VERSION})
add_definitions(-DMAINT_VERSION=${VERSION_INFO_MAINT_VERSION})
add_definitions(-DGIT_SHA1=${GIT_SHA1})

# Flags
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -O3 -pipe -march=native")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -O3 -pipe -march=native")
set(CMAKE_FIND_LIBRARY_SUFFIXES ".a")

# Source Code
file(GLOB SOURCES src/*.cpp)

add_executable (${PROJECT_NAME} ${SOURCES})

# Links
target_link_libraries (${PROJECT_NAME} SatHelper.a)
target_link_libraries (${PROJECT_NAME} pthread)
target_link_libraries (${PROJECT_NAME} correct.a)

if(WIN32)
  target_link_libraries(${PROJECT_NAME} wsock32 ws2_32)
  set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} -static")
  set(CMAKE_EXE_LINKER_FLAGS "-static")
endif()

# Install library
install(TARGETS ${PROJECT_NAME} DESTINATION bin)

# Debian Package
SET(CPACK_GENERATOR "DEB")
SET(CPACK_DEBIAN_PACKAGE_MAINTAINER "Lucas Teske")
SET(CPACK_DEBIAN_PACKAGE_DEPENDS "libSatHelper")
SET(CPACK_DEBIAN_PACKAGE_DESCRIPTION "XRIT Decoder")
INCLUDE(CPack)
