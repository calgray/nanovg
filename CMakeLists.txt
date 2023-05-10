cmake_minimum_required(VERSION 3.12)
set(TARGET_NAME nanovg)

FILE(GLOB_RECURSE SRCS RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} src/*.c)
add_library(${TARGET_NAME}
    ${SRCS}
)

option(NANOVG_VULKAN_IMPLEMENTATION   "Use Vulkan backend?" ON)
option(NANOVG_GL3_IMPLEMENTATION      "Use OpenGL backend?" OFF)
option(NANOVG_GLES3_IMPLEMENTATION    "Use OpenGLES backend?" OFF)

if(NANOVG_VULKAN_IMPLEMENTATION)
  find_package(Vulkan REQUIRED)
  target_link_libraries(${TARGET_NAME} PUBLIC Vulkan::Vulkan)
  target_compile_definitions(${TARGET_NAME} PUBLIC NANOVG_VULKAN_IMPLEMENTATION=1)
endif()

if(NANOVG_GL3_IMPLEMENTATION)
  find_package(OpenGL REQUIRED)
  target_link_libraries(${TARGET_NAME} PUBLIC OpenGL::GL)
  target_compile_definitions(${TARGET_NAME} PUBLIC NANOVG_GL3_IMPLEMENTATION=1)
endif()

if(NANOVG_GLES3_IMPLEMENTATION)
  find_package(OpenGL REQUIRED)
  target_link_libraries(${TARGET_NAME} PUBLIC OpenGL::GLES3)
  target_compile_definitions(${TARGET_NAME} PUBLIC NANOVG_GLES3_IMPLEMENTATION=1)
endif()

target_include_directories(${TARGET_NAME} PUBLIC include)


# Quench warnings while compiling NanoVG
if (CMAKE_COMPILER_IS_GNUCC)
  set_source_files_properties(src/nanovg.c PROPERTIES COMPILE_FLAGS -Wno-unused-result)
elseif(MSVC)
  set_source_files_properties(src/nanovg.c PROPERTIES COMPILE_FLAGS "/wd4005 /wd4456 /wd4457")
endif()

# TODO - examples
