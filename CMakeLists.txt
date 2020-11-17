cmake_minimum_required (VERSION 3.1.3 FATAL_ERROR)
project (etcd-cpp-api)

find_library(CPPREST_LIB NAMES cpprest)
find_path(CPPREST_INCLUDE_DIR NAMES cpprest/http_client.h)

find_package(Boost REQUIRED COMPONENTS system thread locale random)

set (etcd-cpp-api_VERSION_MAJOR 0)
set (etcd-cpp-api_VERSION_MINOR 1)

EXEC_PROGRAM(protoc 
ARGS -I=${CMAKE_CURRENT_SOURCE_DIR}/proto
    --cpp_out=${CMAKE_CURRENT_SOURCE_DIR}/proto
    ${CMAKE_CURRENT_SOURCE_DIR}/proto/*.proto)

EXEC_PROGRAM(protoc 
    ARGS -I=${CMAKE_CURRENT_SOURCE_DIR}/proto
        --grpc_out=${CMAKE_CURRENT_SOURCE_DIR}/proto
        --plugin=protoc-gen-grpc=`which grpc_cpp_plugin`
        ${CMAKE_CURRENT_SOURCE_DIR}/proto/rpc.proto)

enable_testing()
include_directories(SYSTEM ${CPPREST_INCLUDE_DIR} ${Boost_INCLUDE_DIR})
include_directories(${CMAKE_CURRENT_SOURCE_DIR})
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wpedantic -Werror -std=c++11")

add_subdirectory(src)
add_subdirectory(tst)
