cmake_minimum_required(VERSION 3.10)
enable_testing()
project(RCore)

if(ae2f_IS_SHARED)
    set(ae2f_PREFIX_LIB SHARED)
    add_compile_definitions(-D_ae2f_IS_SHARED=1)
else()
    set(ae2f_PREFIX_LIB )
    add_compile_definitions(-D_ae2f_IS_SHARED=0)
endif()

add_library(
    ${PROJECT_NAME} ${ae2f_PREFIX_LIB}
    "include/GED/Core/Clock.h"
    "include/GED/Core/Ctrl/MousePoint.h" 
    "include/GED/Core/Ctrl/Event.h" 

    "src/Ctrl/MousePoint.c"
    "src/Ctrl/Event.c"
    
    "src/xClock.cpp"

    "src/Camera.c"
    "src/xCamera.cpp"

    "src/BmpSrc.c"
)


include(FetchContent)
find_package(Bmp)
if(NOT Bmp_FOUND)
    message("ae2f::Bmp not found")
    add_subdirectory(submodules/Bmp)
    target_link_libraries(${PROJECT_NAME} Bmp)
else()
    target_link_libraries(${PROJECT_NAME} ae2f::Bmp)
endif()


include_directories(include)

target_include_directories(${PROJECT_NAME}
    INTERFACE
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
        $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)

install(TARGETS ${PROJECT_NAME}
    EXPORT ${PROJECT_NAME}Targets
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)

install(DIRECTORY include/
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/
)

install(EXPORT ${PROJECT_NAME}Targets
    FILE ${PROJECT_NAME}Targets.cmake
    NAMESPACE GED::
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake
)

include(CMakePackageConfigHelpers)
configure_package_config_file(
    "${CMAKE_CURRENT_SOURCE_DIR}/Config.cmake.in"
    "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake"
    INSTALL_DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/
)

install(FILES
    "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake"
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake
)

add_subdirectory(test)