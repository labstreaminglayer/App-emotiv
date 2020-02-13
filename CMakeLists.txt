cmake_minimum_required(VERSION 3.15)

cmake_policy(SET CMP0091 NEW)  # https://cmake.org/cmake/help/latest/policy/CMP0091.html#policy:CMP0091

project(Emotiv
	VERSION 0.1)


list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake")


find_package(LSL REQUIRED
      HINTS ${LSL_INSTALL_ROOT}                                   # User-provided option
      "${CMAKE_CURRENT_LIST_DIR}/../../LSL/liblsl/build/"         # Defaults
      "${CMAKE_CURRENT_LIST_DIR}/../../LSL/liblsl/build/install"
      "${CMAKE_CURRENT_LIST_DIR}/../../LSL/liblsl/out/install/x64-Release" # Some defaults for windows visual studio integrated cmake
      "${CMAKE_CURRENT_LIST_DIR}/../../LSL/liblsl/out/install/x64-Debug"  
      PATH_SUFFIXES share/LSL
)


#: Needed for ui_XY.h files
set(CMAKE_INCLUDE_CURRENT_DIR ON)
#: Enable automatic compilation of .cpp->.moc, xy.ui->ui_xy.h and resource files
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTORCC ON)

find_package(Qt5 REQUIRED COMPONENTS Widgets Xml)

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

set(THIRD_PARTY_DLLS
   LSL::lsl
   Qt5::Core
   Qt5::Gui
   Qt5::Widgets
)
foreach(_lib ${THIRD_PARTY_DLLS})
    add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD
        COMMAND ${CMAKE_COMMAND} -E copy_if_different
            $<TARGET_FILE:${_lib}>
            $<TARGET_FILE_DIR:${PROJECT_NAME}>)
endforeach()

installLSLApp(${PROJECT_NAME})
installLSLAuxFiles(${PROJECT_NAME}
	emotiv_config.cfg ${EmotivSDK_BINARIES})

LSLGenerateCPackConfig()
