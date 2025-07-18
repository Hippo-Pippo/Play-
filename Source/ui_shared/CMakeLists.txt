cmake_minimum_required(VERSION 3.10)

set(CMAKE_MODULE_PATH
	${CMAKE_CURRENT_SOURCE_DIR}/../../deps/Dependencies/cmake-modules
	${CMAKE_MODULE_PATH}
)

project(ui_shared)

if(NOT TARGET Framework_Sqlite)
	add_subdirectory(
		${CMAKE_CURRENT_SOURCE_DIR}/../../deps/Framework/build_cmake/FrameworkSqlite
		${CMAKE_CURRENT_BINARY_DIR}/FrameworkSqlite
	)
endif()
list(APPEND SHARED_UI_PROJECT_LIBS Framework_Sqlite)

if(NOT TARGET Framework_Http)
	add_subdirectory(
		${CMAKE_CURRENT_SOURCE_DIR}/../../deps/Framework/build_cmake/FrameworkHttp
		${CMAKE_CURRENT_BINARY_DIR}/FrameworkHttp
	)
endif()
list(APPEND SHARED_UI_PROJECT_LIBS Framework_Http)

find_package(nlohmann_json QUIET)
if(NOT nlohmann_json_FOUND)
	MESSAGE("-- Using Provided nlohmann_json source")
	if(NOT TARGET nlohmann_json)
		add_subdirectory(
			${CMAKE_CURRENT_SOURCE_DIR}/../../deps/Dependencies/nlohmann_json
			${CMAKE_CURRENT_BINARY_DIR}/nlohmann_json
			EXCLUDE_FROM_ALL
		)
	endif()
	list(APPEND SHARED_UI_PROJECT_LIBS nlohmann_json)
else()
	MESSAGE("-- Using System nlohmann_json source")
	list(APPEND SHARED_UI_PROJECT_LIBS nlohmann_json::nlohmann_json)
endif()

if(NOT TARGET PlayCore)
	add_subdirectory(
		${CMAKE_CURRENT_SOURCE_DIR}/../
		${CMAKE_CURRENT_BINARY_DIR}/Source
	)
endif()
list(APPEND SHARED_UI_PROJECT_LIBS PlayCore)

set(SHARED_UI_SRC_FILES
	arcadedrivers/ArcadeDriver.h
	arcadedrivers/NamcoSys147Driver.cpp
	arcadedrivers/NamcoSys147Driver.h
	arcadedrivers/NamcoSys246Driver.cpp
	arcadedrivers/NamcoSys246Driver.h
	ArcadeDefinition.h
	ArcadeDiskCache.cpp
	ArcadeDiskCache.h
	ArcadeUtils.cpp
	ArcadeUtils.h
	BootablesDbClient.cpp
	BootablesDbClient.h
	BootablesProcesses.cpp
	BootablesProcesses.h
	BootableUtils.cpp
	BootableUtils.h
	TheGamesDbClient.cpp
	TheGamesDbClient.h
	StatsManager.cpp
	StatsManager.h
)
if(ENABLE_AMAZON_S3)
	set(SHARED_UI_SRC_FILES
		${SHARED_UI_SRC_FILES}
		AmazonS3Utils.cpp
		AmazonS3Utils.h
	)
endif()
add_library(ui_shared STATIC ${SHARED_UI_SRC_FILES})
target_link_libraries(ui_shared ${SHARED_UI_PROJECT_LIBS})
target_include_directories(ui_shared PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/..)
