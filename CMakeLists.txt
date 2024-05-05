cmake_minimum_required(VERSION 3.16)
project(CMakeSFMLProject VERSION 0.1.3 LANGUAGES CXX)

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
option(BUILD_SHARED_LIBS "Build shared libraries" OFF)

include(FetchContent)
FetchContent_Declare(sfml
    GIT_REPOSITORY https://github.com/SFML/SFML.git
    GIT_TAG master)
if(NOT sfml_POPULATED)
    FetchContent_Populate(sfml)
    add_subdirectory(${sfml_SOURCE_DIR} ${sfml_BINARY_DIR} EXCLUDE_FROM_ALL)
endif()

# Disable console window for `Release` build on Windows, while preserving `main()` entry point
if(WIN32)
    if(MSVC)
        set(SUBSYSTEM_LINKER_OPTIONS "$<IF:$<CONFIG:Debug>,/SUBSYSTEM:CONSOLE,/SUBSYSTEM:WINDOWS;/ENTRY:mainCRTStartup>")
    else()
        set(SUBSYSTEM_LINKER_OPTIONS "$<IF:$<NOT:$<CONFIG:Debug>>,-mwindows,>")
    endif()
endif()

add_executable(CMakeSFMLProject src/main.cpp)
target_link_libraries(CMakeSFMLProject PRIVATE SFML::Graphics)
target_link_options(CMakeSFMLProject PRIVATE ${SUBSYSTEM_LINKER_OPTIONS})

install(TARGETS CMakeSFMLProject)
install(FILES LICENSE.md RENAME CMakeSFMLProject.md DESTINATION licenses)
install(FILES ${sfml_SOURCE_DIR}/license.md RENAME SFML.md DESTINATION licenses)

set(CPACK_PACKAGE_NAME CMakeSFMLProject)
set(CPACK_PACKAGE_VENDOR MyCompany)
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "CMake SFML template project")

set(CPACK_PACKAGE_INSTALL_DIRECTORY ${CPACK_PACKAGE_NAME})
set(CPACK_VERBATIM_VARIABLES TRUE)

if(WIN32)
    set(CPACK_GENERATOR ZIP)
else()
    set(CPACK_GENERATOR TGZ)
endif()

include(CPack)
