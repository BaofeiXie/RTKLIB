cmake_minimum_required(VERSION 3.10)
project(RtkLib_demo5_b34)

# windows: camke -DWIN=ON ..
# Linux:   camke -DWIN=OFF -DCMAKE_BUILD_TYPE=Release/Debug -D_DONLINE=ON -DCMAKE_INSTALL_PREFIX=build path ..

# set build flags
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fPIC")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fPIC")

# set output dir
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/dll)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)

# add define
if (WIN32)
	add_definitions(-DWIN32)
endif()
add_definitions(-D_CRT_SECURE_NO_WARNINGS -DENACMP -DENAGLO -DENAGAL -DENAQZS -DNFREQ=5)

# add code dir
include_directories(${PROJECT_SOURCE_DIR}/src)
file(GLOB src_header "${PROJECT_SOURCE_DIR}/src/*.h")
file(GLOB src_source "${PROJECT_SOURCE_DIR}/src/*.c")
file(GLOB rcv_source_0 "${PROJECT_SOURCE_DIR}/src/rcv/*.c")

# set exclude c
set(exclude_files comnav.c tersus.c)

# 过滤要包含的文件
foreach(file ${rcv_source_0})
    get_filename_component(file_name ${file} NAME)
    list(FIND exclude_files ${file_name} INDEX)
    if(INDEX EQUAL -1)
        list(APPEND rcv_source ${file})
    endif()
endforeach()

# assign file
source_group("Header Files" FILES ${src_header})
source_group("Source Files" FILES ${src_source})
source_group("Source Files/rcv" FILES ${rcv_source})

# add library
add_library(rtklib STATIC ${src_header} ${src_source} ${rcv_source})

if (NOT WIN32)
	target_link_libraries(rtklib m)
endif()

# convbin
add_executable(convbin ${PROJECT_SOURCE_DIR}/app/consapp/convbin/convbin.c)
target_link_libraries(convbin rtklib)
if (WIN32)
	target_link_libraries(convbin winmm.lib ws2_32.lib)
endif()

# rnx2rtkp
add_executable(rnx2rtkp ${PROJECT_SOURCE_DIR}/app/consapp/rnx2rtkp/rnx2rtkp.c)
target_link_libraries(rnx2rtkp rtklib)
if (WIN32)
	target_link_libraries(rnx2rtkp winmm.lib ws2_32.lib)
endif()

# pos2kml
add_executable(pos2kml ${PROJECT_SOURCE_DIR}/app/consapp/pos2kml/pos2kml.c)
target_link_libraries(pos2kml rtklib)
if (WIN32)
	target_link_libraries(pos2kml winmm.lib ws2_32.lib)
endif()

# rnx2rtcm
add_executable(rnx2rtcm ${PROJECT_SOURCE_DIR}/util/rnx2rtcm/rnx2rtcm.c)
target_link_libraries(rnx2rtcm rtklib)
if (WIN32)
	target_link_libraries(rnx2rtcm winmm.lib ws2_32.lib)
endif()

if (NOT WIN32)
# rtkrcv
add_executable(rtkrcv ${PROJECT_SOURCE_DIR}/app/consapp/rtkrcv/rtkrcv.c)
target_link_libraries(rtkrcv rtklib vt)

# str2str
add_executable(str2str ${PROJECT_SOURCE_DIR}/app/consapp/str2str/str2str.c)
target_link_libraries(str2str rtklib)
endif()

