project(MRIWatcher)

cmake_minimum_required(VERSION 2.6)

include(CPack)

###### FIND ITK PACKAGE #########
# find_package(ITK)
#
# if(ITK_FOUND)
#   include(${USE_ITK_FILE})
# else(ITK_FOUND)
#   message("ITK not found. Error !")
# endif(ITK_FOUND)
#
# message("USE_ITK_FILE= " ${USE_ITK_FILE})

find_package(ITK REQUIRED)
include(${ITK_USE_FILE})

#################################

###### FIND QT PACKAGE ##########

find_package(Qt4 REQUIRED)
if(QT_USE_FILE)
  include(${QT_USE_FILE})
  message("QT_USE_FILE=" ${QT_USE_FILE})
  set(QT_USE_QTXML 1)
else(QT_USE_FILE)
  set( QT_LIBRARIES   ${QT_QT_LIBRARY} )
endif(QT_USE_FILE)

################################

###### FIND GLUT PACKAGE #######

# include(${CMAKE_CURRENT_SOURCE_DIR}/CMake/glutSetup.cmake)
find_package(GLUT REQUIRED)

################################

##### Include directories ######

include(${CMAKE_ROOT}/Modules/FindOpenGL.cmake)
include(${CMAKE_ROOT}/Modules/FindGLU.cmake)

include_directories(
  ${QT_INCLUDE_DIR}
  ${GLUT_INCLUDE_DIR}
  ${CMAKE_CURRENT_SOURCE_DIR}/Libraries/IO/Qt
  ${CMAKE_CURRENT_SOURCE_DIR}/Libraries/IO/Common
  ${CMAKE_CURRENT_SOURCE_DIR}/Libraries/Display/Common
  ${CMAKE_CURRENT_SOURCE_DIR}/Libraries/Display/Window2D
  ${CMAKE_CURRENT_SOURCE_DIR}
  ${CMAKE_CURRENT_BINARY_DIR}
  )

#################################

###### Include source files #####

if(WIN32)
 set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} /subsystem:windows")
endif(WIN32)

set(MriWatcher_SRCS
  main.cpp
  mriwatchergui.cpp
  mriwatcherframe.cpp
  imageframegui.cpp
  qtwindow2dfloat.cpp
  imageframelayout.cpp
  mriwatcherapp.cpp
)

if(WIN32)
set(MriWatcher_SRCS
	${MriWatcher_SRCS}
	MriWatcher.rc
)
endif(WIN32)


set(MriWatcher_MOC_SRCS
  mriwatchergui.h
  mriwatcherframe.h
  imageframegui.h
  qtwindow2dfloat.h
  mriwatcherapp.h
)

set(MriWatcher_HEADERS
  imageframelayout.h
)

set(MriWatcher_GUI_SRCS
  mriwatcherform.ui
  imageframeform.ui
)

set(MriWatcher_RES
  icon.qrc
)

QT4_WRAP_CPP(MriWatcher_MOC_HEADERS ${MriWatcher_MOC_SRCS})
QT4_WRAP_UI(MriWatcher_FORMS_HEADERS ${MriWatcher_GUI_SRCS})
QT4_ADD_RESOURCES(MriWatcher_RES_RCC ${MriWatcher_RES})


add_definitions(-DQT_DLL)
add_definitions(-DQT_THREAD_SUPPORT)
add_definitions(${QT_DEFINITIONS})
add_definitions(-DMriWatcher_VERSION="2.2")

add_executable(MriWatcher
  ${MriWatcher_SRCS}
  ${MriWatcher_HEADERS}
  ${MriWatcher_MOC_HEADERS}
  ${MriWatcher_FORMS_HEADERS}
  ${MriWatcher_RES_RCC}
)

message("ITKIO= " ${ITK_LIBRARIES})

target_link_libraries(MriWatcher
  ${GLUT_glut_LIBRARY}
  ${ITK_LIBRARIES}
  ${QT_LIBRARIES}
  ${QT_QTMAIN_LIBRARY}
  ${QT_QTOPENGL_LIBRARY}
  ${OPENGL_glu_LIBRARY}
  ${OPENGL_LIBRARY}
 )

install( TARGETS MriWatcher RUNTIME DESTINATION bin )

