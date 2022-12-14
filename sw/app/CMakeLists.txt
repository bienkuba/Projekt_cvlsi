cmake_minimum_required(VERSION 3.19)

add_executable(app)

target_link_libraries(app PRIVATE
    libs
)

target_link_options(app PRIVATE
    -T ${CMAKE_CURRENT_SOURCE_DIR}/link.ld
)

target_sources(app PRIVATE
    crt0.S
    src/main.cpp
)

add_custom_command(TARGET app POST_BUILD
    COMMAND ${CMAKE_OBJDUMP} -SD app > app.dis
    COMMAND ${CMAKE_OBJCOPY} -O binary app app.bin
    COMMAND srec_cat app.bin -binary -fill 0x00 --within app.bin -binary --range-padding 4 -offset 0x0000 -byte-swap 4 -o app.vmem -vmem
)
