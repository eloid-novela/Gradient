cmake_minimum_required(VERSION 3.21)

project(Gradient LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

find_package(glfw3 CONFIG REQUIRED)
find_package(GLEW CONFIG REQUIRED)
find_package(glm CONFIG REQUIRED)
find_package(Stb REQUIRED)
find_package(imgui CONFIG REQUIRED)
find_package(OpenGL REQUIRED)

add_executable(Gradient main.cpp)

target_include_directories(Gradient PRIVATE "${Stb_INCLUDE_DIR}")
target_link_libraries(Gradient PRIVATE
    glfw
    GLEW::GLEW
    glm::glm
    imgui::imgui
    OpenGL::GL
)

add_custom_command(TARGET Gradient POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_directory
        "${CMAKE_CURRENT_SOURCE_DIR}/images"
        "$<TARGET_FILE_DIR:Gradient>/images"
)

set_property(TARGET Gradient PROPERTY
    VS_DEBUGGER_WORKING_DIRECTORY "$<TARGET_FILE_DIR:Gradient>"
)