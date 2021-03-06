cmake_minimum_required(VERSION 3.1)

project(airsaned)

add_custom_command(
    OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/version.cpp
           ${CMAKE_CURRENT_BINARY_DIR}/_version.cpp
    COMMAND ${CMAKE_COMMAND}
            -DFILE=${CMAKE_CURRENT_BINARY_DIR}/version.cpp
            -P ${CMAKE_CURRENT_SOURCE_DIR}/version.cmake
    WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fno-rtti -Wreturn-type")
set(CMAKE_CXX_FLAGS_MINSIZEREL  "${CMAKE_CXX_FLAGS_MINSIZEREL} -s")
if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE MinSizeRel CACHE STRING "Build type" FORCE)
endif()
set(THREADS_PREFER_PTHREAD_FLAG ON)
find_package(Threads REQUIRED)
include_directories(.)
add_executable(${PROJECT_NAME}
    ${CMAKE_CURRENT_BINARY_DIR}/version.cpp
    server/main.cpp
    server/optionsfile.cpp
    server/scanserver.cpp
    server/mainpage.cpp
    server/scanner.cpp
    server/scanjob.cpp
    server/scannerpage.cpp
    sanecpp/sanecpp.cpp
    basic/uuid.cpp
    basic/dictionary.cpp
    basic/fdbuf.cpp
    web/httpserver.cpp
    web/webpage.cpp
    web/errorpage.cpp
    imageformats/imageencoder.cpp
    imageformats/jpegencoder.cpp
    imageformats/pdfencoder.cpp
    imageformats/pngencoder.cpp
    zeroconf/mdnspublisher.cpp
    zeroconf/hotplugnotifier.cpp
)
target_link_libraries(
    ${PROJECT_NAME}
    Threads::Threads
    sane
    jpeg
    png
    avahi-client
    avahi-common
    usb-1.0
    magic
)
install(TARGETS ${PROJECT_NAME}
    RUNTIME DESTINATION bin
)
install(FILES systemd/airsaned.service
    DESTINATION /lib/systemd/system
)
install(FILES systemd/airsaned.default
    DESTINATION /etc/default
    RENAME airsane
)

