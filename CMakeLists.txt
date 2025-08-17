cmake_minimum_required(VERSION 3.12)

# 使用するツールチェーン（ARM向け）
set(CMAKE_TOOLCHAIN_FILE ../arm_toolchain.cmake)

# プロジェクトの基本設定
project(try-kernel C CXX ASM)
set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 17)

# 一時的にツールチェーンを解除
unset(CMAKE_TOOLCHAIN_FILE CACHE)

# elf2uf2 ツールのビルド（tool/ ディレクトリ）
add_subdirectory(tool)

# 再度ツールチェーンを設定（必要なら）
set(CMAKE_TOOLCHAIN_FILE ../arm_toolchain.cmake)

# try-kernel のビルド対象ソース
add_executable(try-kernel
    application/usermain.c
    boot/boot2.c
    boot/reset_hdr.c
    boot/vector_tbl.c
    device/adc/adc_rp2040.c
    device/devmgr/device_tbl.c
    device/devmgr/device.c
    device/i2c/i2c_rp2040.c
    kernel/context.c
    kernel/eventflag.c
    kernel/gpio.c
    kernel/inittsk.c
    kernel/messagebuf.c
    kernel/scheduler.c
    kernel/semaphore.c
    kernel/syslib.c
    kernel/systimer.c
    kernel/task_mange.c
    kernel/task_queue.c
    kernel/task_sync.c
    kernel/dispatch.S
    kernel/interrupt.c
)

# インクルードディレクトリの指定
target_include_directories(try-kernel PRIVATE include)

# リンカスクリプトの指定
target_link_options(try-kernel PRIVATE -T ${CMAKE_SOURCE_DIR}/linker/pico_memmap.ld)
set_target_properties(try-kernel PROPERTIES LINK_DEPENDS ${CMAKE_SOURCE_DIR}/linker/pico_memmap.ld)

# ビルド後に ELF → UF2 変換を実行
add_custom_command(
    OUTPUT ${CMAKE_BINARY_DIR}/trykernel.uf2
    COMMAND ${CMAKE_SOURCE_DIR}/tool/elf2uf2 ${CMAKE_BINARY_DIR}/try-kernel ${CMAKE_BINARY_DIR}/trykernel.uf2
    DEPENDS try-kernel elf2uf2
    COMMENT "Converting ELF to UF2 (host-side)"
)

add_custom_target(convert_uf2 ALL DEPENDS ${CMAKE_BINARY_DIR}/trykernel.uf2)

add_dependencies(try-kernel elf2uf2)
