cmake_minimum_required(VERSION 3.4)

if (NOT DEFINED ENV{TRAVIS_BUILD_NUMBER})
    set(ENV{TRAVIS_BUILD_NUMBER} 1)
endif()

project(helloworld VERSION 0.0.$ENV{TRAVIS_BUILD_NUMBER})

if (WIN32)
    find_package(BOOST COMPONENTS unit_test_framework REQUIRED)
else()
    find_package(Boost COMPONENTS unit_test_framework REQUIRED)
endif()

if (NOT Boost_FOUND)
    message("___BOOST NOT FOUNT___")
endif()

#include_directories(${Boost_INCLUDE_DIRS})
link_directories(${Boost_LIBRARY_DIRS})

configure_file(version.h.in ${PROJECT_SOURCE_DIR}/version.h)

#---------------------------

add_library(lib lib.cpp)

add_executable(helloworld main.cpp)
add_executable(test_version test_version.cpp)

set_target_properties(lib helloworld test_version PROPERTIES
        CXX_STANDART 17
        CXX_STANDART_REQUIRED ON
if (NOT WIN32)
        COMPILE_OPTIONS "-Wpedantic;-Wall;-Wextra"
else()
)

set_target_properties(test_version PROPERTIES
        COMPILE_DEFINIITIONS BOOST_TEST_DYN_LINK
        INCLUDE_DIRECTORIES ${Boost_INCLUDE_DIR}
)

target_link_libraries (test_version lib ${Boost_LIBRARIES})
target_link_libraries (helloworld lib)

#---------------------------

install (TARGETS helloworld RUNTIME DESTINATION bin)

set(CPACK_GENERATOR DEB)

set(CPACK_PACKAGE_VERSION_MAJOR "${PROJECT_VERSION_MAJOR}")
set(CPACK_PACKAGE_VERSION_MINOR "${PROJECT_VERSION_MINOR}")
set(CPACK_PACKAGE_VERSION_PATCH "${PROJECT_VERSION_PATCH}")

set(CPACK_PACKAGE_CONTACT nikbar93@gmail.com)
set(CPACK_DEBIAN_PACKAGE_SHLIBDEPS ON)

include(CPack)

enable_testing()

add_test(run_tests test_version)


















