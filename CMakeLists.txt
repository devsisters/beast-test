cmake_minimum_required(VERSION 3.10)
include(ExternalProject)
project(beast_server)

set(CMAKE_CXX_STANDARD 14)


if( UNIX )
    set( Boost_url "https://dl.bintray.com/boostorg/release/1.67.0/source/boost_1_67_0.tar.gz")
    set( Boost_Bootstrap_Command ./bootstrap.sh )
    set( Boost_b2_Command ./b2 )
else()
    if( WIN32 )
        set( Boost_url "https://dl.bintray.com/boostorg/release/1.67.0/source/boost_1_67_0.zip")
        set( Boost_Bootstrap_Command cmd /C bootstrap.bat msvc )
        set( Boost_b2_Command b2.exe )
    endif()
endif()

if(CMAKE_SIZEOF_VOID_P EQUAL 8)
    set(Boost_address_model 64)
else()
    set(Boost_address_model 32)
endif()

if (MSVC_VERSION EQUAL 1500) #VS2008
    set(boost_toolset "--toolset=msvc-9.0")
elseif(MSVC_VERSION EQUAL 1600) #VS2010
    set(boost_toolset "--toolset=msvc-10.0")
elseif(MSVC_VERSION EQUAL 1700) #VS2012
    set(boost_toolset "--toolset=msvc-11.0")
elseif(MSVC_VERSION EQUAL 1800) #VS2013
    set(boost_toolset "--toolset=msvc-12.0")
elseif(MSVC_VERSION EQUAL 1900) #VS2015
    set(boost_toolset "--toolset=msvc-14.0")
endif(MSVC_VERSION EQUAL 1500)

set(EXTERNAL_INSTALL_LOCATION ${CMAKE_BINARY_DIR}/thirdparty/boost)
ExternalProject_Add(Boost
        BUILD_IN_SOURCE 1
        URL ${Boost_url}
        UPDATE_COMMAND ""
        CONFIGURE_COMMAND ${Boost_Bootstrap_Command} --prefix=${EXTERNAL_INSTALL_LOCATION}/lib
        BUILD_COMMAND ${Boost_b2_Command} install -j8 --prefix=${EXTERNAL_INSTALL_LOCATION} --with-system address-model=${Boost_address_model} link=static ${boost_toolset}
        INSTALL_COMMAND ""
        )
include_directories(${EXTERNAL_INSTALL_LOCATION}/include)
link_directories(${EXTERNAL_INSTALL_LOCATION}/lib)


add_executable(beast_server main.cpp)
add_dependencies(beast_server Boost)
target_link_libraries(beast_server boost_system.a)