cmake_minimum_required(VERSION 3.0)
project(lisaem)

cmake_host_system_information(RESULT BUILD_HOST QUERY FQDN)
file(READ LICENSE LICENSETEXT)
set(BUILD_USER $ENV{LOGNAME})

find_package(wxWidgets COMPONENTS core base REQUIRED)
include(${wxWidgets_USE_FILE})
include_directories(include ${CMAKE_CURRENT_BINARY_DIR}/include)

file(MAKE_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/cpu68k)
file(COPY ${CMAKE_CURRENT_SOURCE_DIR}/cpu68k/def68k.def 
	DESTINATION ${CMAKE_CURRENT_BINARY_DIR}/cpu68k)

configure_file(include/built_by.h.in
	include/built_by.h
	@ONLY)
add_executable(def68k
	cpu68k/def68k.c
	cpu68k/tab68k.c)
add_executable(gen68k
	cpu68k/gen68k.c
	cpu68k/tab68k.c)
add_custom_command(OUTPUT cpu68k/cpu68k-0.c cpu68k/cpu68k-1.c cpu68k/cpu68k-2.c
	cpu68k/cpu68k-3.c cpu68k/cpu68k-4.c cpu68k/cpu68k-5.c cpu68k/cpu68k-6.c 
	cpu68k/cpu68k-7.c cpu68k/cpu68k-8.c cpu68k/cpu68k-9.c cpu68k/cpu68k-a.c 
	cpu68k/cpu68k-b.c cpu68k/cpu68k-c.c cpu68k/cpu68k-d.c cpu68k/cpu68k-e.c
	cpu68k/cpu68k-f.c
	COMMAND gen68k 
	WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/cpu68k)
add_custom_command(OUTPUT 
	${CMAKE_CURRENT_BINARY_DIR}/cpu68k/def68k-funcs.h 
	${CMAKE_CURRENT_BINARY_DIR}/cpu68k/def68k-iibs.h 
	${CMAKE_CURRENT_BINARY_DIR}/cpu68k/def68k-proto.h
	COMMAND def68k
	DEPENDS ${CMAKE_CURRENT_BINARY_DIR}/cpu68k/def68k.def
	WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/cpu68k)

add_library(cpu68k STATIC
	cpu68k/tab68k.c
	${CMAKE_CURRENT_BINARY_DIR}/cpu68k/cpu68k-0.c
	${CMAKE_CURRENT_BINARY_DIR}/cpu68k/cpu68k-1.c
	${CMAKE_CURRENT_BINARY_DIR}/cpu68k/cpu68k-2.c
	${CMAKE_CURRENT_BINARY_DIR}/cpu68k/cpu68k-3.c
	${CMAKE_CURRENT_BINARY_DIR}/cpu68k/cpu68k-4.c
	${CMAKE_CURRENT_BINARY_DIR}/cpu68k/cpu68k-5.c
	${CMAKE_CURRENT_BINARY_DIR}/cpu68k/cpu68k-6.c
	${CMAKE_CURRENT_BINARY_DIR}/cpu68k/cpu68k-7.c
	${CMAKE_CURRENT_BINARY_DIR}/cpu68k/cpu68k-8.c
	${CMAKE_CURRENT_BINARY_DIR}/cpu68k/cpu68k-9.c
	${CMAKE_CURRENT_BINARY_DIR}/cpu68k/cpu68k-a.c
	${CMAKE_CURRENT_BINARY_DIR}/cpu68k/cpu68k-b.c
	${CMAKE_CURRENT_BINARY_DIR}/cpu68k/cpu68k-c.c
	${CMAKE_CURRENT_BINARY_DIR}/cpu68k/cpu68k-d.c
	${CMAKE_CURRENT_BINARY_DIR}/cpu68k/cpu68k-e.c
	${CMAKE_CURRENT_BINARY_DIR}/cpu68k/cpu68k-f.c
	include/vars.h
	include/generator.h
	${CMAKE_CURRENT_BINARY_DIR}/cpu68k/def68k-iibs.h
	${CMAKE_CURRENT_BINARY_DIR}/cpu68k/def68k-funcs.h
	${CMAKE_CURRENT_BINARY_DIR}/cpu68k/def68k-proto.h)
target_include_directories(cpu68k PRIVATE ${CMAKE_CURRENT_BINARY_DIR}/cpu68k)

add_library(generator STATIC
	generator/cpu68k.c
	generator/reg68k.c
	generator/diss68k.c
	generator/ui_log.c
	${CMAKE_CURRENT_BINARY_DIR}/include/built_by.h
	${CMAKE_CURRENT_BINARY_DIR}/cpu68k/def68k-iibs.h
	include/vars.h)
target_include_directories(generator PRIVATE ${CMAKE_CURRENT_BINARY_DIR}/cpu68k)
target_link_libraries(generator cpu68k)

add_executable(lisadiskinfo
	lisa/lisadiskinfo.c
	lisa/libdc42.c)
add_executable(lisafsh-tool
	lisa/lisafsh-tool.c
	lisa/libdc42.c)

add_executable(lisaem 
	lisa/libdc42.c
	lisa/floppy.c
	lisa/profile.c
	lisa/unvars.c
	lisa/vars.c
	lisa/glue.c
	lisa/fliflo_queue.c
	lisa/cops.c
	lisa/zilog8530.c
	lisa/via6522.c
	lisa/irq.c
	lisa/mmu.c
	lisa/rom.c
	lisa/romless.c
	lisa/memory.c
	lisa/symbols.c
	wxui/lisaem_wx.cpp
	wxui/imagewriter-wx.cpp
	wxui/LisaConfig.cpp
	wxui/LisaConfigFrame.cpp
	)
target_link_libraries(lisaem generator ${wxWidgets_LIBRARIES})
target_include_directories(lisaem PRIVATE wxui)
