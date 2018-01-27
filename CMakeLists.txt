cmake_minimum_required (VERSION 3.0)
message("--------------------")
set_property(GLOBAL PROPERTY USE_FOLDERS ON)
project(MarkPi)
set(CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}")

set(ARM_FLAGS "-ftree-vectorize;-mfpu=fp-armv8;-Werror;-Wno-psabi;-Wall;-Wno-error=comment")

get_filename_component(SEARCH_ROOT1 "${CMAKE_SOURCE_DIR}" DIRECTORY)
get_filename_component(SEARCH_ROOT "${SEARCH_ROOT1}" DIRECTORY)
get_filename_component(SEARCH_ROOT2 "${SEARCH_ROOT}" DIRECTORY)
message("SEARCH_ROOT_1: ${SEARCH_ROOT1}")
message("SEARCH_ROOT: ${SEARCH_ROOT}")

#######
# RASPIRAW
find_path(VC_DIR REQUIRED NAMES vc_display_types.h HINTS ${CMAKE_SYSROOT}/usr/include/interface/vctypes)
if (NOT VC_DIR)
    message(FATAL_ERROR "unable to find VC_DIR")
endif()
set(RASPIRAW_DIR ./)
set(RASPI_SRC ${RASPIRAW_DIR}/raspiraw.c ${RASPIRAW_DIR}/RaspiCLI.c)
set(RASPI_HDR ${RASPIRAW_DIR}/adv7282m_modes.h
    ${RASPIRAW_DIR}/imx219_modes.h
    ${RASPIRAW_DIR}/ov5647_modes.h
    ${RASPIRAW_DIR}/raw_header.h
    ${RASPIRAW_DIR}/RaspiCLI.h
)
add_executable(raspiraw ${RASPI_SRC} ${RASPI_HDR})
    target_include_directories(raspiraw PUBLIC ${VC_DIR} ${RASPIRAW_DIR})
    target_link_libraries(raspiraw mmal_core mmal_util mmal_vc_client vcos bcm_host ${OpenCV_LIBS})
    target_compile_options(raspiraw PRIVATE ${ARM_FLAGS})
