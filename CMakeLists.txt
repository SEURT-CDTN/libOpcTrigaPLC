cmake_minimum_required(VERSION 3.12)

include(FetchContent)

project(
  libOpcTrigaPLC
  VERSION 0.0.0
  DESCRIPTION "Comunicate with the PLC in the Triga Control Table using OPC"
  HOMEPAGE_URL "https://github.com/SEURT-CDTN/libOpcTrigaPLC"
  LANGUAGES CXX)

FetchContent_Declare(
  open62541pp
  GIT_REPOSITORY https://github.com/open62541pp/open62541pp
  GIT_TAG master
  EXCLUDE_FROM_ALL)
FetchContent_MakeAvailable(open62541pp)

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set(CMAKE_C_STANDARD_INCLUDE_DIRECTORIES
    ${CMAKE_C_IMPLICIT_INCLUDE_DIRECTORIES})

set(LIBOPCTRIGAPLC_SRC src/libOpcTrigaPLC.cpp)

add_library(opcTrigaPLC ${LIBOPCTRIGAPLC_SRC})
add_library(opcTrigaPLC::opcTrigaPLC ALIAS opcTrigaPLC)

target_include_directories(opcTrigaPLC PUBLIC ${PROJECT_SOURCE_DIR}/include)
target_link_libraries(opcTrigaPLC PUBLIC open62541pp)

add_executable(opctrigaplc-test src/test.cpp)
target_link_libraries(opctrigaplc-test PRIVATE opcTrigaPLC)

install(DIRECTORY ${PROJECT_SOURCE_DIR}/include/
        DESTINATION ${CMAKE_INSTALL_PREFIX}/include/libOpcTrigaPLC/)
install(
  TARGETS opcTrigaPLC
  LIBRARY DESTINATION ${CMAKE_INSTALL_PREFIX}/lib
  ARCHIVE DESTINATION ${CMAKE_INSTALL_PREFIX}/lib
  RUNTIME DESTINATION ${CMAKE_INSTALL_PREFIX}/bin)

install(DIRECTORY ${open62541pp_SOURCE_DIR}/include/
        DESTINATION ${CMAKE_INSTALL_PREFIX}/include/open62541pp/)
install(
  TARGETS open62541pp
  LIBRARY DESTINATION ${CMAKE_INSTALL_PREFIX}/lib
  ARCHIVE DESTINATION ${CMAKE_INSTALL_PREFIX}/lib
  RUNTIME DESTINATION ${CMAKE_INSTALL_PREFIX}/bin)

install(DIRECTORY ${open62541pp_SOURCE_DIR}/3rdparty/open62541/include/
        DESTINATION ${CMAKE_INSTALL_PREFIX}/include/open62541/)
install(
  TARGETS open62541
  LIBRARY DESTINATION ${CMAKE_INSTALL_PREFIX}/lib
  ARCHIVE DESTINATION ${CMAKE_INSTALL_PREFIX}/lib
  RUNTIME DESTINATION ${CMAKE_INSTALL_PREFIX}/bin)

install(TARGETS opctrigaplc-test
        RUNTIME DESTINATION ${CMAKE_INSTALL_PREFIX}/bin)

set(LibOpcTrigaPLC_INCLUDE_DIRS ${CMAKE_INSTALL_PREFIX}/include/libOpcTrigaPLC)
set(LibOpcTrigaPLC_LIBRARIES
    ${CMAKE_INSTALL_PREFIX}/lib/libopcTrigaPLC.a
    ${CMAKE_INSTALL_PREFIX}/lib/libopen62541pp.a
    ${CMAKE_INSTALL_PREFIX}/lib/libopen62541.a)
configure_file(
  ${CMAKE_CURRENT_SOURCE_DIR}/resources/LibOpcTrigaPLCConfig.cmake.in
  ${CMAKE_CURRENT_BINARY_DIR}/resources/LibOpcTrigaPLCConfig.cmake @ONLY)
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/resources/LibOpcTrigaPLCConfig.cmake
        DESTINATION ${CMAKE_INSTALL_PREFIX}/lib/cmake/LibOpcTrigaPLC)
