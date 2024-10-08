set(PROGRAM pico9918)

#add_subdirectory(pio-utils)

add_executable(${PROGRAM})

if (DEFINED PICO9918_NO_CLOCKS)
  set(PICO9918_CLOCK_SOURCES "")
  add_definitions(-DPICO9918_NO_CLOCKS)
else ()
  set(PICO9918_CLOCK_SOURCES "clocks.pio.h")
endif ()

target_sources(${PROGRAM} PRIVATE
	main.c palette.c ${PICO9918_CLOCK_SOURCES} tms9918.pio.h)

# generate image array source files from png images
visrealm_generate_image_source_ram(${PROGRAM} splash res/splash.png )

# generate header file from pio
pico_generate_pio_header(${PROGRAM} ${CMAKE_CURRENT_LIST_DIR}/clocks.pio)
pico_generate_pio_header(${PROGRAM} ${CMAKE_CURRENT_LIST_DIR}/tms9918.pio)

pico_add_extra_outputs(${PROGRAM})
pico_enable_stdio_usb(${PROGRAM} 0)
pico_enable_stdio_uart(${PROGRAM} 0)

pico_set_binary_type(${PROGRAM} copy_to_ram)   # TOO SLOW TO BOOT

add_definitions(-DPICO_DISABLE_SHARED_IRQ_HANDLERS=1)
add_definitions(-DPICO_TIME_DEFAULT_ALARM_POOL_DISABLED=1)
add_definitions(-DPICO_PANIC_FUNCTION=)

target_link_libraries(${PROGRAM} PUBLIC
        pico_stdlib
        pico_multicore
        hardware_pio        
        pico9918-vga
        vrEmuTms9918)

add_subdirectory(pio-utils)
add_subdirectory(vga)
