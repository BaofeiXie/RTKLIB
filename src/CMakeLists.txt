include_directories(.)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/bin)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/dll)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/lib)

if(WIN32)
	add_definitions(-DWIN_DLL)
else()
	add_definitions(-DDLL)
endif()

if (GCC)
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -std=c99 -Wall -O3 -ansi -pedantic")
    set(CMAKE_C_FLAGS "-Wno-unused-but-set-variable -Wno-format-overflow -Wno-unused-result -Wpointer-to-int-cast")
endif()

if ("${CMAKE_BUILD_TYPE}" STREQUAL "Release")
    if (GCC)
        set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS} -O3 -fsee -fomit-frame-pointer -fno-signed-zeros -fno-math-errno -funroll-loops")
    endif()
endif()

if(MSVC)
    add_definitions(-D_CRT_SECURE_NO_WARNINGS)
endif()

# add codes
include_directories(${CMAKE_CURRENT_SOURCE_DIR})
file(GLOB HEAD_RTKLIB "${CMAKE_CURRENT_SOURCE_DIR}/*.h")
file(GLOB SRCS_RTKLIB "${CMAKE_CURRENT_SOURCE_DIR}/*.c")
file(GLOB SRCS_RTKLIB_RCV "${CMAKE_CURRENT_SOURCE_DIR}/rcv/*.c")

# delete rcv/tersus.c
list(REMOVE_ITEM SRCS_RTKLIB_RCV 
	"${CMAKE_CURRENT_SOURCE_DIR}/rcv/tersus.c"
	"${CMAKE_CURRENT_SOURCE_DIR}/rcv/comnav.c"
)

# assign file
source_group("Header Files" FILES ${HEAD_RTKLIB})
source_group("Source Files" FILES ${SRCS_RTKLIB})
source_group("Source Files/rcv" FILES ${SRCS_RTKLIB_RCV})

# add library
add_library(rtklib SHARED ${HEAD_RTKLIB} ${SRCS_RTKLIB} ${SRCS_RTKLIB_RCV})

target_include_directories(rtklib
    PUBLIC ${PROJECT_SOURCE_DIR}/include
)

if(WIN32)
	if(MSVC)
		# stack option
		math(EXPR STACK_SIZE "20 * 1024 * 1024")
		target_link_options(rtklib PRIVATE /STACK:${STACK_SIZE})
		# heap option
		math(EXPR HEAP_SIZE "200 * 1024 * 1024")
		target_link_options(rtklib PRIVATE /HEAP:${HEAP_SIZE})
		# message
		message(STATUS "rtklib MSVC stack size set to 20MB, heap size set to 20MB")
	endif()
	target_link_libraries(rtklib wsock32 ws2_32 winmm)
	add_definitions(-DWIN_DLL)
else()
	target_link_libraries(rtklib m pthread)
	add_definitions(-DDLL)
endif()
install(TARGETS rtklib RUNTIME DESTINATION lib)
