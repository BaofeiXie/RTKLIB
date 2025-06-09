include_directories(${PROJECT_SOURCE_DIR}/src)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/bin)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/dll)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/lib)

add_executable(rnx2rtcm rnx2rtcm/rnx2rtcm.c)
target_link_libraries(rnx2rtcm rtklib)
install(TARGETS rnx2rtcm RUNTIME DESTINATION bin)

# add additional libraries to compile on windows
if(WIN32)
	if(MSVC)
		# stack option
		math(EXPR STACK_SIZE "400 * 1024 * 1024")
		target_link_options(rnx2rtcm PRIVATE /STACK:${STACK_SIZE})
		# heap option
		math(EXPR HEAP_SIZE "4 * 1024 * 1024 * 1024")
		target_link_options(rnx2rtcm PRIVATE /HEAP:${HEAP_SIZE})
		# message
		message(STATUS "rnx2rtcm MSVC stack size set to 400MB, heap size set to 4GB")
	endif()
  target_link_libraries(rnx2rtcm wsock32 ws2_32 winmm)
endif()

