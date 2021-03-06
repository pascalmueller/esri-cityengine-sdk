cmake_minimum_required(VERSION 2.8.10)
cmake_policy(SET CMP0015 NEW)


### project definition

set(PROJECT prt4cmd)
project(${PROJECT})


### dependencies

find_package(Boost COMPONENTS system program_options filesystem)
include_directories(${Boost_INCLUDE_DIR})
link_directories(${Boost_LIBRARY_DIRS})

if(NOT prt_DIR)
	set(prt_DIR "${PROJECT_SOURCE_DIR}/../../../prt/cmake")
endif()
find_package(prt CONFIG)
set(CESDK_VERSION "cesdk_${PRT_VERSION_MAJOR}_${PRT_VERSION_MINOR}_${PRT_VERSION_MICRO}")


### build target

add_executable(${PROJECT} prt4cmd.cpp)

SET(CMAKE_INSTALL_RPATH "")
SET(CMAKE_INSTALL_RPATH_USE_LINK_PATH FALSE)

include_directories(${PRT_INCLUDE_PATH})
target_link_libraries(${PROJECT} ${Boost_LIBRARIES} ${PRT_LIBRARY})

if(${CMAKE_SYSTEM_NAME} STREQUAL "Linux")
	set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -Wl,-rpath,'$ORIGIN'")
endif()


### install target

set(CMAKE_INSTALL_PREFIX "${PROJECT_SOURCE_DIR}/../install" CACHE PATH "default install prefix" FORCE) 

install(CODE "file(REMOVE_RECURSE ${CMAKE_INSTALL_PREFIX}/*)")
install(PROGRAMS ${CMAKE_BINARY_DIR}/prt4cmd${CMAKE_EXECUTABLE_SUFFIX} DESTINATION bin)
install(FILES ${PRT_LIBRARIES} DESTINATION bin OPTIONAL)
install(FILES ${PRT_EXT_LIBRARIES} DESTINATION lib)
install(CODE "FILE(MAKE_DIRECTORY ${CMAKE_INSTALL_PREFIX}/output)")
install(DIRECTORY ${PROJECT_SOURCE_DIR}/../../../license DESTINATION .)
if(WIN32)
	install(FILES ${PROJECT_SOURCE_DIR}/../README.windows DESTINATION .)
else()
	install(FILES ${PROJECT_SOURCE_DIR}/../README.linux_osx DESTINATION .)
endif()


### package target

string(TOLOWER ${CMAKE_SYSTEM_NAME} PACKAGE_SYSTEM)
if(CMAKE_CXX_SIZEOF_DATA_PTR EQUAL 8)
	set(PACKAGE_ARCH "x86_64")
else()
	set(PACKAGE_ARCH "x86")
endif()

set(CPACK_PACKAGE_NAME					"esri_prt4cmd")
set(CPACK_PACKAGE_VENDOR				"Esri R&D Zurich Inc.")
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY	"Command Line Example for CityEngine SDK")
set(CPACK_PACKAGE_VERSION_MAJOR			${PRT4CMD_VERSION_MAJOR})
set(CPACK_PACKAGE_VERSION_MINOR			${PRT4CMD_VERSION_MINOR})
set(CPACK_PACKAGE_VERSION_PATCH			${PRT4CMD_VERSION_MICRO})
set(CPACK_PACKAGE_INSTALL_DIRECTORY		"${CPACK_PACKAGE_NAME}_${PACKAGE_SYSTEM}_${PACKAGE_ARCH}")
set(CPACK_PACKAGE_FILE_NAME				"${CPACK_PACKAGE_INSTALL_DIRECTORY}")

if(${CMAKE_SYSTEM_NAME} STREQUAL "Windows")
	set(CPACK_GENERATOR					ZIP)
	set(CPACK_SOURCE_GENERATOR			ZIP)
	set(CPACK_ZIP_COMPONENT_INSTALL		1)
elseif(${CMAKE_SYSTEM_NAME} STREQUAL "Darwin")
	set(CPACK_GENERATOR					ZIP)
	set(CPACK_SOURCE_GENERATOR			ZIP)
	set(CPACK_TGZ_COMPONENT_INSTALL		1)
elseif(${CMAKE_SYSTEM_NAME} STREQUAL "Linux")
	set(CPACK_GENERATOR					ZIP)
	set(CPACK_SOURCE_GENERATOR			ZIP)
	set(CPACK_TGZ_COMPONENT_INSTALL		1)
endif()

include(CPack)
