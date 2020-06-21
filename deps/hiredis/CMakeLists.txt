add_library(hiredis STATIC
        hiredis.c
        async.c
        net.c
        read.c
        sds.c
        )