# Based on: https://github.com/tamaskenez/allthesmallthings/blob/master/s/imgui/CMakeLists.txt

cmake_minimum_required(VERSION 3.20)
project(imgui)

set(CMAKE_DEBUG_POSTFIX d)
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED)

set(ROOT ${CMAKE_CURRENT_SOURCE_DIR})

add_library(imgui STATIC
        ${ROOT}/imgui.cpp
        ${ROOT}/imgui.h
        ${ROOT}/imstb_rectpack.h
        ${ROOT}/imstb_textedit.h
        ${ROOT}/imstb_truetype.h
        ${ROOT}/imgui_demo.cpp
        ${ROOT}/imgui_draw.cpp
        ${ROOT}/imgui_internal.h
        ${ROOT}/imgui_tables.cpp
        ${ROOT}/imgui_widgets.cpp
        ${ROOT}/misc/cpp/imgui_stdlib.cpp
)
target_include_directories(imgui PUBLIC
        $<BUILD_INTERFACE:${ROOT}>
        $<INSTALL_INTERFACE:include>)

function(create_backend BACKEND)
    set(NAME imgui_impl_${BACKEND})
    set(HEADER ${ROOT}/backends/${NAME}.h)

    add_library(${NAME} STATIC ${ROOT}/backends/${NAME}.cpp ${HEADER})

    target_link_libraries(${NAME} PUBLIC imgui)
    target_include_directories(${NAME} PUBLIC
            $<BUILD_INTERFACE:${ROOT}/backends>
            $<INSTALL_INTERFACE:include>)

    LIST(APPEND INSTALL_TARGETS ${NAME})
    LIST(APPEND INSTALL_HEADERS ${HEADER})
endfunction()

create_backend(glfw)

target_link_libraries(imgui_impl_glfw PRIVATE glfw)
    
create_backend(vulkan)
    
find_package(Vulkan REQUIRED)
target_link_libraries(imgui_impl_vulkan PRIVATE ${Vulkan_LIBRARIES})
target_include_directories(imgui_impl_vulkan PUBLIC ${Vulkan_INCLUDE_DIRS})
