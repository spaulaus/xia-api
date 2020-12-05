cmake_minimum_required(VERSION 3.13)
project(xia-api)

set(CMAKE_INSTALL_PREFIX ${CMAKE_INSTALL_PREFIX}/xia/pixie/sdk CACHE PATH "Install Prefix" FORCE)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -g -Wall -DPLX_LITTLE_ENDIAN -DPCI_CODE -DPLX_LINUX")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -g -Wall -DPLX_LITTLE_ENDIAN -DPCI_CODE -DPLX_LINUX")

#------------- BUILD SYS --------------------------
add_library(Pixie16Sys SHARED sys/communication.c sys/i2cm24c64.c sys/pixie16sys.c sys/tools.c)
target_include_directories(Pixie16Sys PUBLIC sys vendor/analog_devices $ENV{PLX_SDK_DIR}/Include/)
target_link_directories(Pixie16Sys PUBLIC $ENV{PLX_SDK_DIR}/PlxApi/Library)
target_link_libraries(Pixie16Sys PUBLIC :PlxApi.a)

file(GLOB SYS_PUBLIC_HEADERS sys/*.h)
set_target_properties(Pixie16Sys PROPERTIES PUBLIC_HEADER "${SYS_PUBLIC_HEADERS}")
install(TARGETS Pixie16Sys LIBRARY DESTINATION lib PUBLIC_HEADER DESTINATION include)

#------------- BUILD APP --------------------------
add_library(Pixie16App SHARED app/pixie16app.c app/utilities.c)
target_include_directories(Pixie16App PUBLIC app sys vendor/analog_devices $ENV{PLX_SDK_DIR}/Include/)
target_link_directories(Pixie16App PUBLIC $ENV{PLX_SDK_DIR}/PlxApi/Library)
target_link_libraries(Pixie16App PUBLIC Pixie16Sys :PlxApi.a)

file(GLOB APP_PUBLIC_HEADERS app/*.h)
set_target_properties(Pixie16App PROPERTIES PUBLIC_HEADER "${APP_PUBLIC_HEADERS}")
install(TARGETS Pixie16App LIBRARY DESTINATION lib PUBLIC_HEADER DESTINATION include)