cmake_minimum_required(VERSION 3.12)

# pull in PICO SDK (must be before project)
include(pico_sdk_import.cmake)

# pull in helpers for my custom tools
include(visrealm_tools.cmake)

set(BUILD_SHARED_LIBS OFF)

set(PROJECT pico9918)

if (PICO9918_CONFIG)
  message("Using PICO9918_CONFIG '${PICO9918_CONFIG}'")
  include(${PICO9918_CONFIG})
endif()

#
# Defaults if they're not set in the config file.
#
if (DEFINED PICO9918_RESTRICTED_HW_RPI_PICO)
  #
  # This is short-hand for "this is going to be run in a genuine
  # Raspberry Pi Pico".  Force the board type to "pico" in this
  # case, and disable the clock outputs (the Pico doesn't have
  # enough GPIO pins for that).
  #
  message("PICO9918_RESTRICTED_HW_RPI_PICO specified in config file")
  set(PICO9918_BOARD "pico")
  set(PICO9918_NO_CLOCKS 1)
endif ()

if (DEFINED PICO9918_BOARD)
  message("Using PICO9918_BOARD '${PICO9918_BOARD}' from config file")
else ()
  set(PICO9918_BOARD "pico9918_v04")
  message("Using default PICO9918_BOARD '${PICO9918_BOARD}'")
endif ()

if (DEFINED PICO9918_VGA_SYNC_PINS_START)
  message("Using PICO9918_VGA_SYNC_PINS_START '${PICO9918_VGA_SYNC_PINS_START}' from config file")
else ()
  set(PICO9918_VGA_SYNC_PINS_START "0")
  message("Using default PICO9918_VGA_SYNC_PINS_START '${PICO9918_VGA_SYNC_PINS_START}'")
endif ()

if (DEFINED PICO9918_VGA_RGB_PINS_START)
  message("Using PICO9918_VGA_RGB_PINS_START '${PICO9918_VGA_RGB_PINS_START}' from config file")
else ()
  set(PICO9918_VGA_RGB_PINS_START "2")
  message("Using default PICO9918_VGA_RGB_PINS_START '${PICO9918_VGA_RGB_PINS_START}'")
endif ()

if (DEFINED PICO9918_INT_ACTIVE_HIGH)
  message("Using active-high interrupt output from config file")
  add_definitions(-DPICO9918_INT_ACTIVE_HIGH)
else ()
  message("Using default active-low interrupt output")
endif ()

if (DEFINED PICO9918_NO_CLOCKS)
  message("Disabling clock outputs per config file")
  # Logic for handling this is in src/CMakeLists.txt.
endif ()

set(PICO_BOARD_HEADER_DIRS ${CMAKE_CURRENT_LIST_DIR}/src/boards )
set(PICO_BOARD ${PICO9918_BOARD})

project(${PROJECT} C CXX)

add_definitions(-DPICO_BUILD=1)
add_definitions(-DVR_EMU_TMS9918_SINGLE_INSTANCE=1)
add_definitions(-DVGA_SYNC_PINS_START=${PICO9918_VGA_SYNC_PINS_START})
add_definitions(-DVGA_RGB_PINS_START=${PICO9918_VGA_RGB_PINS_START})

set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 17)

# Initialize the Pico SDK
pico_sdk_init()

add_subdirectory(submodules/vrEmuTms9918)
add_subdirectory(src)
add_subdirectory(test)

