cmake_minimum_required(VERSION 3.0)

project(openblas VERSION 0.2.14)
string(TOUPPER ${PROJECT_NAME} PROJECT_NAME_UPPER)

option(BUILD_${PROJECT_NAME_UPPER} "Build ${PROJECT_NAME}" ${CB_BUILD_ALL})
# OpenBLAS needs to be built as a shared library on Windows
set(${PROJECT_NAME_UPPER}_BUILD_SHARED_LIBS ON CACHE BOOL "Build shared libraries for ${PROJECT_NAME}" FORCE)
set(${PROJECT_NAME_UPPER}_URL_BASE "http://downloads.sourceforge.net/project/openblas/v${PROJECT_VERSION}" CACHE STRING "Set the base download url for ${PROJECT_NAME}")

set(${PROJECT_NAME_UPPER}_DEPENDS )

if(BUILD_${PROJECT_NAME_UPPER})

    set(${PROJECT_NAME_UPPER}_CONFIG
"
# ${PROJECT_NAME_UPPER} config
set(${PROJECT_NAME_UPPER}_INCLUDE_DIR \${CMAKE_CURRENT_LIST_DIR}/include CACHE PATH \"\")
set(${PROJECT_NAME}_INCLUDE_DIR \${CMAKE_CURRENT_LIST_DIR}/include CACHE PATH \"\")
set(OpenBLAS_INCLUDE_DIR \${CMAKE_CURRENT_LIST_DIR}/include CACHE PATH \"\")
set(${PROJECT_NAME_UPPER}_LIB \${CMAKE_CURRENT_LIST_DIR}/lib/libopenblas.dll.a CACHE FILEPATH \"\")
set(${PROJECT_NAME}_LIB \${CMAKE_CURRENT_LIST_DIR}/lib/libopenblas.dll.a CACHE FILEPATH \"\")
set(OpenBLAS_LIB \${CMAKE_CURRENT_LIST_DIR}/lib/libopenblas.dll.a CACHE FILEPATH \"\")
"
    PARENT_SCOPE)

    include(ExternalProject)

    if(CMAKE_SIZEOF_VOID_P STREQUAL "8")
        # 64 bit
        set(_openblas_url ${${PROJECT_NAME_UPPER}_URL_BASE}/OpenBLAS-v0.2.14-Win64-int32.zip)
        set(_openblas_hash URL_MD5 bb59507959975d8d55f3e7eb0ecd5ea3)
        set(_mingw_url ${${PROJECT_NAME_UPPER}_URL_BASE}/mingw64_dll.zip)
        set(_mingw_hash URL_MD5 e619f1f936638240472397e0f7970e66)
    else()
        # 32 bit
        set(_openblas_url ${${PROJECT_NAME_UPPER}_URL_BASE}/OpenBLAS-v0.2.14-Win32.zip)
        set(_mingw_url ${${PROJECT_NAME_UPPER}_URL_BASE}/mingw32_dll.zip)
   endif()

    ExternalProject_Add(
        ${PROJECT_NAME}_download
        # URL ${_openblas_url}
        URL ${CMAKE_CURRENT_SOURCE_DIR}/../../dependencies/OpenBLAS-v0.2.14-Win64-int32.zip
        ${_openblas_hash}
        DOWNLOAD_DIR ${CB_DOWNLOAD_DIR}
        CONFIGURE_COMMAND ""
        BUILD_COMMAND ""
        INSTALL_COMMAND ${CMAKE_COMMAND} -E copy_directory bin ${CMAKE_INSTALL_PREFIX}/bin
        COMMAND ${CMAKE_COMMAND} -E copy_directory lib ${CMAKE_INSTALL_PREFIX}/lib
        COMMAND ${CMAKE_COMMAND} -E copy_directory include ${CMAKE_INSTALL_PREFIX}/include
        BUILD_IN_SOURCE 1
    )
    list(APPEND ${PROJECT_NAME}_targets ${PROJECT_NAME}_download)

    ExternalProject_Get_Property("${PROJECT_NAME}_download" SOURCE_DIR)

    ExternalProject_Add(
        ${PROJECT_NAME}_download_mingw
        # URL ${_mingw_url}
        URL ${CMAKE_CURRENT_SOURCE_DIR}/../../dependencies/mingw64_dll.zip
        ${_mingw_hash}
        DOWNLOAD_DIR ${CB_DOWNLOAD_DIR}
        CONFIGURE_COMMAND ""
        BUILD_COMMAND ""
        INSTALL_COMMAND ${CMAKE_COMMAND} -E copy_directory . ${CMAKE_INSTALL_PREFIX}/bin
        BUILD_IN_SOURCE 1
    )

    list(APPEND ${PROJECT_NAME}_targets ${PROJECT_NAME}_download_mingw)

    ExternalProject_Get_Property("${PROJECT_NAME}_download" SOURCE_DIR)

    add_custom_target(${PROJECT_NAME})
    add_dependencies(${PROJECT_NAME} ${${PROJECT_NAME}_targets})
endif()