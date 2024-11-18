cmake_minimum_required(VERSION 3.30)

project(quickjs)

file(STRINGS "./quickjs/VERSION" QUICKJS_VERSION_CONTENT)
list(GET QUICKJS_VERSION_CONTENT 0 QUICKJS_VERSION)

add_library(qjs)
target_link_libraries(qjs pthread)
target_compile_definitions(qjs PRIVATE CONFIG_VERSION="${QUICKJS_VERSION}" CONFIG_BIGNUM)
if(WIN32)
    target_compile_definitions(qjs PRIVATE CONFIG_WIN32)
endif()
target_sources(qjs PRIVATE
    "./quickjs/cutils.c"
    "./quickjs/libregexp.c"
    "./quickjs/libunicode.c"
    "./quickjs/libbf.c"
    "./quickjs/quickjs.c"
    "./quickjs/quickjs-libc.c"
    "./quickjs/repl.c"
    "./quickjs/qjscalc.c"
)

# 创建动态库 qjs
add_library(qjsdll SHARED
    "./quickjs/cutils.c"
    "./quickjs/libregexp.c"
    "./quickjs/libunicode.c"
    "./quickjs/libbf.c"
    "./quickjs/quickjs.c"
    "./quickjs/quickjs-libc.c"
    "./quickjs/repl.c"
    "./quickjs/qjscalc.c"
)
target_link_libraries(qjsdll pthread)
target_compile_definitions(qjsdll PRIVATE CONFIG_VERSION="${QUICKJS_VERSION}" CONFIG_BIGNUM)
if(WIN32)
    target_compile_definitions(qjsdll PRIVATE CONFIG_WIN32)
endif()

add_executable(qjsc)
target_link_libraries(qjsc qjs)
target_compile_definitions(qjsc PRIVATE CONFIG_VERSION="${QUICKJS_VERSION}" CONFIG_BIGNUM)
if(WIN32)
    target_compile_definitions(qjsc PRIVATE CONFIG_WIN32)
endif()
target_sources(qjsc PRIVATE
    "./quickjs/qjsc.c"
)

add_executable(qjs-app)
target_link_libraries(qjs-app qjs)
target_compile_definitions(qjs-app PRIVATE CONFIG_VERSION="${QUICKJS_VERSION}" CONFIG_BIGNUM)
if(WIN32)
    target_compile_definitions(qjs-app PRIVATE CONFIG_WIN32)
endif()
target_sources(qjs-app PRIVATE
    "./quickjs/qjs.c"
)
