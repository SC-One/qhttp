cmake_minimum_required(VERSION 3.14)

set(PRJ_ROOT_NAME qhttp)
project(${PRJ_ROOT_NAME} LANGUAGES C CXX)

set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(QT NAMES Qt6 Qt5 REQUIRED COMPONENTS Network Core)
find_package(Qt${QT_VERSION_MAJOR} REQUIRED COMPONENTS Network Core)
#find_package(qhttpserver)
#message("qhttpserver_FOUND: ${qhttpserver_FOUND}")

set(src_dir ${CMAKE_CURRENT_SOURCE_DIR}/src)
set(ThirdParty_Dir ${CMAKE_CURRENT_SOURCE_DIR}/3rdparty)

set(hdrs
  ${src_dir}/qhttpfwd.hpp
  ${src_dir}/qhttpabstracts.hpp
  ${src_dir}/qhttpserverconnection.hpp
  ${src_dir}/qhttpserverrequest.hpp
  ${src_dir}/qhttpserverresponse.hpp
  ${src_dir}/qhttpserver.hpp
)
set(srcs
  ${src_dir}/qhttpabstracts.cpp
  ${src_dir}/qhttpserverconnection.cpp
  ${src_dir}/qhttpserverrequest.cpp
  ${src_dir}/qhttpserverresponse.cpp
  ${src_dir}/qhttpserver.cpp
)

option(QHTTP_DYNAMIC_LIB "Shared library?" ON)
set(Build_Type SHARED)
if(${QHTTP_DYNAMIC_LIB})
  set(Build_Type SHARED)
else()
  set(Build_Type STATIC)
endif()

option(QHTTP_HAS_CLIENT "Build clients?" ON)
if(${QHTTP_HAS_CLIENT})
  set(hdrs ${hdrs}
    ${src_dir}/qhttpclient.hpp
    ${src_dir}/qhttpclientresponse.hpp
    ${src_dir}/qhttpclientrequest.hpp
  )
else()
  set(srcs ${srcs}
    ${src_dir}/qhttpclientrequest.cpp
    ${src_dir}/qhttpclientresponse.cpp
    ${src_dir}/qhttpclient.cpp
  )
endif()


add_library(${PRJ_ROOT_NAME} ${Build_Type}
  ${ThirdParty_Dir}/http-parser/http_parser.h
  ${ThirdParty_Dir}/http-parser/http_parser.c

  ${hdrs}
  ${srcs}

)


if(WIN32)
  target_compile_definitions(${PRJ_ROOT_NAME} QHTTP_EXPORT)
else()
endif()
target_link_libraries(${PRJ_ROOT_NAME} PRIVATE
  Qt${QT_VERSION_MAJOR}::Core
  Qt${QT_VERSION_MAJOR}::Network
)

target_include_directories(${PRJ_ROOT_NAME} PUBLIC
  ${src_dir}
  ${ThirdParty_Dir}
)

#install(TARGETS ${PRJ_ROOT_NAME}
#    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR})
