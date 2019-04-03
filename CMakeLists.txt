cmake_minimum_required(VERSION 3.12)

project(rcssmonitor
    VERSION "15.2.1"
    DESCRIPTION "RoboCup Simulation Soccer Monitor"
    HOMEPAGE_URL "https://rcsoccersim.github.io"
)

find_package(Qt4 REQUIRED)
find_package(Boost "1.32.0" REQUIRED COMPONENTS "program_options")

include(CheckLibraryExists)
include(CheckSymbolExists)
include(CheckIncludeFile)

check_library_exists("z" "deflate" "" HAVE_LIBZ)
if (HAVE_LIBZ)
    set(HAVE_DEFLATE 1 CACHE INTERNAL "Indicates whether deflate() function exists.")
endif ()

check_library_exists("m" "sqrt" "" HAVE_LIBM)
check_include_file("math.h" HAVE_MATH_H)
if (HAVE_LIBM AND HAVE_MATH_H)
    set(HAVE_SQRT 1 CACHE INTERNAL "Indicates whether sqrt() function exists.")
    set(old_CMAKE_REQUIRED_LIBRARIES "${CMAKE_REQUIRED_LIBRARIES}")
    set(CMAKE_REQUIRED_LIBRARIES "m")
    check_symbol_exists("pow" "math.h" HAVE_POW)
    check_symbol_exists("rint" "math.h" HAVE_RINT)
    set(CMAKE_REQUIRED_LIBRARIES "${old_CMAKE_REQUIRED_LIBRARIES}")
    unset(old_CMAKE_REQUIRED_LIBRARIES)
endif ()

check_include_file("time.h" HAVE_TIME_H)
check_symbol_exists("memset" "string.h" HAVE_MEMSET)
check_symbol_exists("strtol" "stdlib.h" HAVE_STRTOL)
check_include_file("netinet/in.h" HAVE_NETINET_IN_H)
set(HAVE_BOOST_PROGRAM_OPTIONS 1 CACHE INTERNAL "Indicates whether program_options Boost module is available")

configure_file(
    "${PROJECT_SOURCE_DIR}/config.h.in"
    "${PROJECT_BINARY_DIR}/config.h"
    @ONLY
)
add_compile_definitions("HAVE_CONFIG_H=1" "VERSION=\"${PROJECT_VERSION}\"")
include_directories("${PROJECT_BINARY_DIR}")
set(CMAKE_AUTOMOC ON)

# ==============================================================================
# RCSS Monitor Build Plan
# ==============================================================================
add_executable(rcssmonitor
    "src/rcsslogplayer/parser.cpp"
    "src/rcsslogplayer/types.cpp"
    "src/rcsslogplayer/util.cpp"
    "src/angle_deg.cpp"
    "src/ball_painter.cpp"
    "src/circle_2d.cpp"
    "src/config_dialog.cpp"
    "src/disp_holder.cpp"
    "src/draw_info_painter.cpp"
    "src/field_canvas.cpp"
    "src/field_painter.cpp"
    "src/line_2d.cpp"
    "src/log_player.cpp"
    "src/main_window.cpp"
    "src/monitor_client.cpp"
    "src/options.cpp"
    "src/player_painter.cpp"
    "src/player_type_dialog.cpp"
    "src/score_board_painter.cpp"
    "src/team_graphic.cpp"
    "src/team_graphic_painter.cpp"
    "src/vector_2d.cpp"
    "src/main.cpp"
)

target_compile_definitions(
    rcssmonitor
    PRIVATE
        "PACKAGE_NAME=\"rcssmonitor\""
        "PACKAGE=\"rcssmonitor\""
)

target_include_directories(
    rcssmonitor
    PRIVATE
        "${PROJECT_SOURCE_DIR}"
        "${PROJECT_SOURCE_DIR}/src"
        "${PROJECT_SOURCE_DIR}/src/rcsslogplayer"
)

target_link_libraries(
    rcssmonitor
    PRIVATE
        Qt4::QtCore
        Qt4::QtGui
        Qt4::QtNetwork
        Boost::boost
        Boost::program_options
)

