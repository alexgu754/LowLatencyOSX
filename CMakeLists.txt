cmake_minimum_required(VERSION 3.28)
project(sdl3gpu)

set(CMAKE_CXX_STANDARD 20)

set(SDL_STATIC ON CACHE BOOL "" FORCE)
set(SDL_SHARED OFF CACHE BOOL "" FORCE)

add_subdirectory(SDL)
add_executable(sdl3gpu main.cpp

        #imgui
        imgui/imgui.cpp
        imgui/imgui_draw.cpp
        imgui/imgui_widgets.cpp
        imgui/imgui_tables.cpp
        imgui/imgui_demo.cpp
        imgui/backends/imgui_impl_metal.mm
        imgui/backends/imgui_impl_osx.mm
        imgui/backends/imgui_impl_sdl3.cpp
        imgui/backends/imgui_impl_sdlgpu3.cpp

)

# Link Core Video framework to your target
target_include_directories(${PROJECT_NAME} PUBLIC 3rdparty/SDL/include)
target_link_libraries(${PROJECT_NAME} PUBLIC ${COREVIDEO_LIBRARY})
target_include_directories(${PROJECT_NAME} PUBLIC
        ${spirv-cross_SOURCE_DIR}/include
        imgui
        imgui/backends
)

target_link_libraries(${PROJECT_NAME} PUBLIC
        SDL3::SDL3

)
