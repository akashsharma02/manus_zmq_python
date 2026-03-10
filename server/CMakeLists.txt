cmake_minimum_required(VERSION 3.11)
project(manus_zmq_server CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Find ZMQ
find_package(PkgConfig REQUIRED)
pkg_check_modules(ZMQ REQUIRED libzmq)

# Fetch nlohmann/json (header-only)
include(FetchContent)
FetchContent_Declare(
    json
    GIT_REPOSITORY https://github.com/nlohmann/json.git
    GIT_TAG v3.11.3
    GIT_SHALLOW TRUE
)
FetchContent_Declare(
    cppzmq
    GIT_REPOSITORY https://github.com/zeromq/cppzmq.git
    GIT_TAG v4.10.0
    GIT_SHALLOW TRUE
)
set(CPPZMQ_BUILD_TESTS OFF CACHE BOOL "" FORCE)
FetchContent_MakeAvailable(json cppzmq)

add_executable(manus_zmq_server
    src/main.cpp
    src/manus_zmq_server.cpp
)

target_include_directories(manus_zmq_server PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}/../ManusSDK/include
)

target_link_directories(manus_zmq_server PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}/../ManusSDK/lib
)

target_link_libraries(manus_zmq_server
    ManusSDK_Integrated
    cppzmq
    nlohmann_json::nlohmann_json
)

# Set RPATH so the binary finds ManusSDK .so at runtime
set_target_properties(manus_zmq_server PROPERTIES
    INSTALL_RPATH "$ORIGIN/../../ManusSDK/lib"
    BUILD_RPATH "${CMAKE_CURRENT_SOURCE_DIR}/../ManusSDK/lib"
)

install(TARGETS manus_zmq_server DESTINATION bin)
