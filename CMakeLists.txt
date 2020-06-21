cmake_minimum_required(VERSION 3.0 FATAL_ERROR)
project(redis VERSION 5.0.0)
set(CMAKE_BUILD_TYPE "Debug")

get_filename_component(REDIS_ROOT "${CMAKE_CURRENT_SOURCE_DIR}" ABSOLUTE)

add_subdirectory(deps)
#add_subdirectory(src/modules)

set(SRC_SERVER_TMP
        src/release.c src/config.c src/module.c src/debug.c src/memtest.c
        #
        src/sds.c src/dict.c src/adlist.c
        src/zipmap.c src/ziplist.c src/intset.c src/quicklist.c
        src/geo.c src/geohash.c src/geohash_helper.c
        src/rax.c src/listpack.c
        #
        src/object.c src/t_hash.c src/t_list.c src/t_set.c src/t_string.c src/t_zset.c src/t_stream.c
        src/ae.c src/anet.c src/networking.c
        src/rio.c src/syncio.c
        src/zmalloc.c
        # persistence
        src/rdb.c src/aof.c src/redis-check-aof.c src/redis-check-rdb.c
        #
        src/pubsub.c src/notify.c src/multi.c src/scripting.c
        src/replication.c src/sentinel.c  src/cluster.c
        src/evict.c src/defrag.c src/lazyfree.c
        # command
        src/db.c src/sort.c src/expire.c src/bitops.c src/latency.c src/hyperloglog.c src/slowlog.c src/lolwut.c src/lolwut5.c
        # utils
        src/endianconv.c src/util.c src/sha1.c src/siphash.c src/crc16.c src/crc64.c src/lzf_c.c src/lzf_d.c src/pqsort.c src/rand.c src/bio.c src/blocked.c
        src/setproctitle.c src/sparkline.c src/localtime.c src/childinfo.c
        )
set(SRC_SERVER src/server.c ${SRC_SERVER_TMP})

set(SRC_CLI src/redis-cli.c
        src/release.c
        src/adlist.c src/dict.c
        src/ae.c src/anet.c
        src/crc64.c src/crc16.c src/siphash.c
        src/zmalloc.c
        )


set(EXECUTABLE_OUTPUT_PATH src)
link_directories(deps/linenoise/ deps/lua/src deps/hiredis)

# redis-server
add_executable(redis-server ${SRC_SERVER})
target_include_directories(redis-server
        PRIVATE ${REDIS_ROOT}/deps/linenoise
        PRIVATE ${REDIS_ROOT}/deps/hiredis
        PRIVATE ${REDIS_ROOT}/deps/lua/src)
target_link_libraries(redis-server
        PRIVATE pthread
        PRIVATE dl
        PRIVATE m
        PRIVATE lua
        PRIVATE linenoise
        PRIVATE hiredis)

# redis-cli
add_executable(redis-cli ${SRC_CLI})
target_include_directories(redis-cli
        PRIVATE ${REDIS_ROOT}/deps/linenoise
        PRIVATE ${REDIS_ROOT}/deps/hiredis
        PRIVATE ${REDIS_ROOT}/deps/lua/src)
target_link_libraries(redis-cli
        PRIVATE pthread
        PRIVATE m
        PRIVATE linenoise
        PRIVATE hiredis)