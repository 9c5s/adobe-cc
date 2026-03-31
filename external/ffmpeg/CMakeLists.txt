cmake_minimum_required(VERSION 3.12.0 FATAL_ERROR)
project(ffmpeg)

# could put a build step here
# ExternalProject_Add(
#    PREFIX
#        FFmpeg
#
#)

add_library(libavcodec STATIC IMPORTED GLOBAL)
add_library(libavformat STATIC IMPORTED GLOBAL)
add_library(libavutil STATIC IMPORTED GLOBAL)

set_target_properties(libavcodec
    PROPERTIES
        IMPORTED_LOCATION ${CMAKE_CURRENT_LIST_DIR}/FFmpeg/libavcodec/libavcodec.a
)
set_target_properties(libavformat
    PROPERTIES
        IMPORTED_LOCATION ${CMAKE_CURRENT_LIST_DIR}/FFmpeg/libavformat/libavformat.a
)
set_target_properties(libavutil
    PROPERTIES
        IMPORTED_LOCATION ${CMAKE_CURRENT_LIST_DIR}/FFmpeg/libavutil/libavutil.a
)

find_library(CoreVideo CoreVideo)

target_link_libraries(libavutil
    INTERFACE $<$<PLATFORM_ID:Darwin>:${CoreVideo}>
)

if(APPLE)
    find_library(ICONV_LIB iconv)
    find_library(ZLIB_LIB z)
    target_link_libraries(libavcodec INTERFACE ${ICONV_LIB} ${ZLIB_LIB})
    target_link_libraries(libavformat INTERFACE ${ZLIB_LIB})
endif()

target_include_directories(libavcodec BEFORE INTERFACE
    ${CMAKE_CURRENT_LIST_DIR}/FFmpeg
)
target_include_directories(libavformat BEFORE INTERFACE
    ${CMAKE_CURRENT_LIST_DIR}/FFmpeg
)
target_include_directories(libavutil BEFORE INTERFACE
    ${CMAKE_CURRENT_LIST_DIR}/FFmpeg
)

add_library(ffmpeg::libavcodec ALIAS libavcodec)
add_library(ffmpeg::libavformat ALIAS libavformat)
add_library(ffmpeg::libavutil ALIAS libavutil)

# add_include(ffmpeg ${CMAKE_CURRENT_LIST_DIR}/FFmpeg)
