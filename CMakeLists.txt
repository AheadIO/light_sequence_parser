cmake_minimum_required(VERSION 3.9.0)

set(LSP_PROJECT "sequenceparser")

file(GLOB_RECURSE LSP_SRC_FILES RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} src/*.cpp)
file(GLOB_RECURSE LSP_HDR_FILES RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} include/*.hpp;include/*.h)

if (LSS_BUILD_SHARED)
    add_library(${LSP_PROJECT} SHARED ${LIBRARY_TYPE} ${LSP_SRC_FILES} ${LEONARDOQT_HDR_FILES} ${LSP_HDR_FILES})
else ()
    add_library(${LSP_PROJECT} STATIC ${LIBRARY_TYPE} ${LSP_SRC_FILES} ${LEONARDOQT_HDR_FILES} ${LSP_HDR_FILES})
endif()

set_property(TARGET ${LSP_PROJECT} PROPERTY INTERFACE_INCLUDE_DIRECTORIES ${CMAKE_CURRENT_SOURCE_DIR}/include)
set_property(TARGET ${LSP_PROJECT} PROPERTY CXX_STANDARD 11)
target_compile_definitions(${LSP_PROJECT} PUBLIC -DNOMINMAX)

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/include)

if(USE_TESTS)

enable_testing()
find_package(GTest REQUIRED)

file(GLOB_RECURSE LSP_TEST_FILES RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} test/*.cpp)
add_executable(${LSP_PROJECT}_test ${LSP_TEST_FILES})

#include_directories(${gtest_SOURCE_DIR}/include ${gtest_SOURCE_DIR})

SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -pthread")
target_link_libraries(${LSP_PROJECT}_test ${GTEST_MAIN_LIBRARIES} ${GTEST_LIBRARIES})

add_test(${LSP_PROJECT} ${LSP_PROJECT}_test)

endif(USE_TESTS)

#-------------------------------------------------------------------------------
# Install
#-------------------------------------------------------------------------------

install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include
        DESTINATION .
        FILES_MATCHING PATTERN "*.hpp"
        PATTERN "*.h"
)

INSTALL( TARGETS
         ${LSP_PROJECT}
         DESTINATION lib
         COMPONENT Runtime
)
