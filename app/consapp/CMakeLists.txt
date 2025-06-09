include_directories(${PROJECT_SOURCE_DIR}/src)
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)

add_executable(rnx2rtkp rnx2rtkp/rnx2rtkp.c)
target_link_libraries(rnx2rtkp rtklib)
install(TARGETS rnx2rtkp RUNTIME DESTINATION bin)

add_executable(convbin convbin/convbin.c)
target_link_libraries(convbin rtklib)
install(TARGETS convbin RUNTIME DESTINATION bin)

add_executable(pos2kml pos2kml/pos2kml.c)
target_link_libraries(pos2kml rtklib)
install(TARGETS pos2kml RUNTIME DESTINATION bin)

# add executables only supported on unix-like systems
if (UNIX)
	add_executable(rtkrcv rtkrcv/rtkrcv.c rtkrcv/vt.c)
	target_link_libraries(rtkrcv rtklib)
	install(TARGETS rtkrcv RUNTIME DESTINATION bin)

	add_executable(str2str str2str/str2str.c)
	target_link_libraries(str2str rtklib)
	install(TARGETS str2str RUNTIME DESTINATION bin)
endif()

# add additional libraries to compile on windows
if(WIN32)
	if(MSVC)
		# stack option
		math(EXPR STACK_SIZE "400 * 1024 * 1024")
		target_link_options(rnx2rtkp PRIVATE /STACK:${STACK_SIZE})
		# heap option
		math(EXPR HEAP_SIZE "4 * 1024 * 1024 * 1024")
		target_link_options(rnx2rtkp PRIVATE /HEAP:${HEAP_SIZE})
		# message
		message(STATUS "rnx2rtkp MSVC stack size set to 400MB, heap size set to 4GB")
	endif()
	target_link_libraries(rnx2rtkp wsock32 ws2_32 winmm)
	
	if(MSVC)
		# stack option
		math(EXPR STACK_SIZE "200 * 1024 * 1024")
		target_link_options(convbin PRIVATE /STACK:${STACK_SIZE})
		# heap option
		math(EXPR HEAP_SIZE "2 * 1024 * 1024 * 1024")
		target_link_options(convbin PRIVATE /HEAP:${HEAP_SIZE})
		# message
		message(STATUS "convbin MSVC stack size set to 200MB, heap size set to 2GB")
	endif()
	target_link_libraries(convbin wsock32 ws2_32 winmm)
	
	if(MSVC)
		# stack option
		math(EXPR STACK_SIZE "200 * 1024 * 1024")
		target_link_options(pos2kml PRIVATE /STACK:${STACK_SIZE})
		# heap option
		math(EXPR HEAP_SIZE "2 * 1024 * 1024 * 1024")
		target_link_options(pos2kml PRIVATE /HEAP:${HEAP_SIZE})
		# message
		message(STATUS "pos2kml MSVC stack size set to 200MB, heap size set to 2GB")
	endif()
	target_link_libraries(pos2kml wsock32 ws2_32 winmm)
endif()

