# Copyright 2017, akashche at redhat.com
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
# http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

cmake_minimum_required ( VERSION 2.8.12 )
project ( pq C )
set ( ${PROJECT_NAME}_SOURCE_DIR ${CMAKE_CURRENT_LIST_DIR}/../../postgresql/src/interfaces/libpq )
set ( ${PROJECT_NAME}_INCLUDE_DIR ${CMAKE_CURRENT_LIST_DIR}/../../postgresql/src/include )

# standalone build
if ( NOT DEFINED STATICLIB_TOOLCHAIN )
    if ( NOT DEFINED ${PROJECT_NAME}_TOOLCHAIN )
        if ( WIN32 )
            if ( CMAKE_SIZEOF_VOID_P EQUAL 8 )
                set ( ${PROJECT_NAME}_TOOLCHAIN "windows_amd64_msvc" )
            else ( )
                set ( ${PROJECT_NAME}_TOOLCHAIN "windows_i386_msvc" )
            endif ( )
        elseif ( APPLE )
            set ( ${PROJECT_NAME}_TOOLCHAIN "macosx_amd64_clang" )
        elseif ( UNIX )
            set ( ${PROJECT_NAME}_TOOLCHAIN "linux_amd64_gcc" )
        else ( )
            message ( FATAL_ERROR "Cannot determine toolchain to use, please specify [${PROJECT_NAME}_TOOLCHAIN]" )
        endif ( )
    endif ( )
else ( )
    set ( ${PROJECT_NAME}_TOOLCHAIN ${STATICLIB_TOOLCHAIN} )
endif ( )

if ( ${PROJECT_NAME}_TOOLCHAIN MATCHES "linux_amd64_[^_]+" )
    set ( ${PROJECT_NAME}_PLATFORM_INCLUDE_DIR
            ${CMAKE_CURRENT_LIST_DIR}/../include_linux_amd64 )
    set ( ${PROJECT_NAME}_PLATFORM_DEFINITIONS
            -D_REENTRANT
            -D_THREAD_SAFE
            -D_POSIX_PTHREAD_SEMANTICS
            -DFRONTEND
            -DUNSAFE_STAT_OK
            -D_GNU_SOURCE
            -DSO_MAJOR_VERSION=5 )
endif ( )

# copy headers
file ( COPY
        ${${PROJECT_NAME}_SOURCE_DIR}/libpq-fe.h
        ${${PROJECT_NAME}_PLATFORM_INCLUDE_DIR}/pg_config.h
        ${${PROJECT_NAME}_PLATFORM_INCLUDE_DIR}/pg_config_ext.h
        ${${PROJECT_NAME}_PLATFORM_INCLUDE_DIR}/pg_config_os.h
        ${${PROJECT_NAME}_PLATFORM_INCLUDE_DIR}/pg_config_paths.h
        DESTINATION ${CMAKE_CURRENT_BINARY_DIR}/include )

# check deplibs cache
if ( STATICLIB_USE_DEPLIBS_CACHE )
    set ( ${PROJECT_NAME}_CACHED_LIB_PATH ${STATICLIB_DEPLIBS_CACHE_DIR}/${CMAKE_STATIC_LIBRARY_PREFIX}${PROJECT_NAME}${CMAKE_STATIC_LIBRARY_SUFFIX} )
    if ( EXISTS ${${PROJECT_NAME}_CACHED_LIB_PATH} )
        add_library( ${PROJECT_NAME} STATIC IMPORTED GLOBAL )
        set_target_properties( ${PROJECT_NAME} PROPERTIES IMPORTED_LOCATION ${${PROJECT_NAME}_CACHED_LIB_PATH} )
        message ( STATUS "Using cached library: [${${PROJECT_NAME}_CACHED_LIB_PATH}]" )
        return ( )
    endif ( )
endif ( )

# library
add_library ( ${PROJECT_NAME} STATIC        
        ${${PROJECT_NAME}_SOURCE_DIR}/fe-auth.c
        ${${PROJECT_NAME}_SOURCE_DIR}/fe-connect.c
        ${${PROJECT_NAME}_SOURCE_DIR}/fe-exec.c
        ${${PROJECT_NAME}_SOURCE_DIR}/fe-misc.c
        ${${PROJECT_NAME}_SOURCE_DIR}/fe-print.c
        ${${PROJECT_NAME}_SOURCE_DIR}/fe-lobj.c
        ${${PROJECT_NAME}_SOURCE_DIR}/fe-protocol2.c
        ${${PROJECT_NAME}_SOURCE_DIR}/fe-protocol3.c
        ${${PROJECT_NAME}_SOURCE_DIR}/pqexpbuffer.c
        ${${PROJECT_NAME}_SOURCE_DIR}/fe-secure.c
        ${${PROJECT_NAME}_SOURCE_DIR}/libpq-events.c
        ${${PROJECT_NAME}_SOURCE_DIR}/../../../src/port/chklocale.c
        ${${PROJECT_NAME}_SOURCE_DIR}/../../../src/port/inet_net_ntop.c
        ${${PROJECT_NAME}_SOURCE_DIR}/../../../src/port/noblock.c
        ${${PROJECT_NAME}_SOURCE_DIR}/../../../src/port/pgstrcasecmp.c
        ${${PROJECT_NAME}_SOURCE_DIR}/../../../src/port/pqsignal.c
        ${${PROJECT_NAME}_SOURCE_DIR}/../../../src/port/thread.c
        ${${PROJECT_NAME}_SOURCE_DIR}/../../../src/port/strlcpy.c
        ${${PROJECT_NAME}_SOURCE_DIR}/../../../src/port/getpeereid.c
        ${${PROJECT_NAME}_SOURCE_DIR}/../../../src/backend/libpq/ip.c
        ${${PROJECT_NAME}_SOURCE_DIR}/../../../src/backend/libpq/md5.c
        ${${PROJECT_NAME}_SOURCE_DIR}/../../../src/backend/utils/mb/encnames.c
        ${${PROJECT_NAME}_SOURCE_DIR}/../../../src/backend/utils/mb/wchar.c )

target_include_directories ( ${PROJECT_NAME} BEFORE PRIVATE
        ${${PROJECT_NAME}_SOURCE_DIR}
        ${${PROJECT_NAME}_INCLUDE_DIR}
        ${${PROJECT_NAME}_PLATFORM_INCLUDE_DIR} )

target_compile_definitions ( ${PROJECT_NAME} PRIVATE ${${PROJECT_NAME}_PLATFORM_DEFINITIONS} )