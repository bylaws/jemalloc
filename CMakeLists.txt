cmake_minimum_required(VERSION 3.14)
set (PROJECT_NAME FEX_jemalloc)
project(${PROJECT_NAME}
  VERSION 5.2.1
  LANGUAGES C CXX)

set(CMAKE_POSITION_INDEPENDENT_CODE ON)
cmake_policy(SET CMP0083 NEW) # Follow new PIE policy
include(CheckPIESupported)
check_pie_supported()

set(CMAKE_INCLUDE_CURRENT_DIR ON)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/pregen/include/)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/include/)

set (SRCS
src/jemalloc.c
src/arena.c
src/background_thread.c
src/base.c
src/bin.c
src/bitmap.c
src/ckh.c
src/ctl.c
src/div.c
src/extent.c
src/extent_dss.c
src/extent_mmap.c
src/hash.c
src/hook.c
src/large.c
src/log.c
src/malloc_io.c
src/mutex.c
src/mutex_pool.c
src/nstime.c
src/pages.c
src/prng.c
src/prof.c
src/rtree.c
src/safety_check.c
src/stats.c
src/sc.c
src/sz.c
src/tcache.c
src/test_hooks.c
src/ticker.c
src/tsd.c
src/witness.c
src/jemalloc_cpp.cpp
)

function(AddLibrary Name Type)
  add_library(${Name} ${Type} ${SRCS})
  set_property(TARGET ${Name} PROPERTY C_STANDARD 11)
  set_property(TARGET ${Name} PROPERTY CXX_STANDARD 17)

  target_link_libraries(${Name} m stdc++ pthread dl)
  set_target_properties(${Name} PROPERTIES OUTPUT_NAME FEX_jemalloc)

  target_include_directories(${Name} PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include/)
  target_include_directories(${Name} PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/pregen/include/)

  target_compile_definitions(${Name} PRIVATE -D_GNU_SOURCE -D_REENTRANT)

  target_compile_options(${Name} PRIVATE -fvisibility=hidden)
endfunction()

AddLibrary(${PROJECT_NAME} STATIC)
AddLibrary(${PROJECT_NAME}_shared SHARED)

install(TARGETS ${PROJECT_NAME} ${PROJECT_NAME}_shared)
