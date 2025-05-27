# CMakeLists.txt for vgui_dll on x86 Linux

cmake_minimum_required(VERSION 3.10)
project(vgui_dll)

set(CMAKE_CXX_STANDARD 98)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
# Set the output directory for binaries
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

# Compiler flags
#set(BASE_CFLAGS -DNDEBUG -D_LINUX -DLINUX -Dstricmp=strcasecmp -D_vsnprintf=vsnprintf)
#set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${BASE_CFLAGS} -w -m32 -O1 -fPIC")

# Include directories
include_directories(
    ${CMAKE_SOURCE_DIR}/include
)

# Source files
file(GLOB VGUI_SOURCES
        "${CMAKE_SOURCE_DIR}/lib-src/vgui/*.cpp"
)
if(CMAKE_HOST_SYSTEM_NAME MATCHES "Linux")
    message("current platform: Linux ")
    include_directories(
            ${CMAKE_SOURCE_DIR}/lib-src/linux
    )
    file(GLOB VGUI_SOURCES_LINUX
            "${CMAKE_SOURCE_DIR}/lib-src/linux/*.cpp"
    )
    list(APPEND VGUI_SOURCES ${VGUI_SOURCES_LINUX})
    list(APPEND CMAKE_CXX_FLAGS "-DNDEBUG -D_LINUX -DLINUX -Dstricmp=strcasecmp -D_vsnprintf=vsnprintf")
elseif(CMAKE_HOST_SYSTEM_NAME MATCHES "Windows")
    message("current platform: Windows ")
    include_directories(
            ${CMAKE_SOURCE_DIR}/lib-src/win32
    )
    file(GLOB VGUI_SOURCES_WIN32
            "${CMAKE_SOURCE_DIR}/lib-src/win32/*.cpp"
    )
    list(APPEND VGUI_SOURCES ${VGUI_SOURCES_WIN32})
else()
    message("current platform: macOS ")
    include_directories(
            ${CMAKE_SOURCE_DIR}/lib-src/osx
    )
    file(GLOB VGUI_SOURCES_OSX
            "${CMAKE_SOURCE_DIR}/lib-src/osx/*.cpp"
    )
    list(APPEND VGUI_SOURCES ${VGUI_SOURCES_OSX})
endif()

# Create shared library
add_library(vgui SHARED ${VGUI_SOURCES})

install(TARGETS vgui DESTINATION lib)

if(WIN32)
    set(CPACK_GENERATOR "ZIP")
    set(CPACK_PACKAGE_FILE_NAME "vgui-${CMAKE_SYSTEM_NAME}-${CMAKE_CXX_COMPILER_ARCHITECTURE_ID}")
else()
    set(CPACK_GENERATOR "TGZ")
    set(CPACK_PACKAGE_FILE_NAME "vgui-${CMAKE_SYSTEM_NAME}-${CMAKE_SYSTEM_PROCESSOR}")
endif()

set(CPACK_INCLUDE_TOPLEVEL_DIRECTORY OFF)

include(CPack)