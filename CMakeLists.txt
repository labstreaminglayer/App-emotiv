cmake_minimum_required(VERSION 3.5)
project(Emotiv
	VERSION 0.1)
include(Findliblsl.cmake)

set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTORCC ON)
find_package(Qt5 REQUIRED COMPONENTS Xml Widgets)

# Boost
# add_definitions(-DBOOST_REGEX_NO_LIB)
find_package(Boost REQUIRED COMPONENTS
	thread
	regex
)

# Emotiv SDK
find_package(EmotivSDK)

add_executable(${PROJECT_NAME} MACOSX_BUNDLE WIN32
	main.cpp
	mainwindow.cpp
	mainwindow.h
	mainwindow.ui
)
target_include_directories(${PROJECT_NAME}
	PRIVATE
	${EmotivSDK_INCLUDE_DIRS}
)
target_link_libraries(${PROJECT_NAME}
	PRIVATE
	Qt5::Widgets
    Qt5::Xml
	LSL::lsl
	Boost::thread
	Boost::regex
	${EmotivSDK_LIBRARIES}
)

installLSLApp(${PROJECT_NAME})
installLSLAuxFiles(${PROJECT_NAME}
	emotiv_config.cfg ${EmotivSDK_BINARIES})
LSLGenerateCPackConfig()
