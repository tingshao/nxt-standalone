# Copyright 2017 The Dawn Authors
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

set(UTILS_DIR ${CMAKE_CURRENT_SOURCE_DIR})

list(APPEND UTILS_SOURCES
    ${UTILS_DIR}/BackendBinding.cpp
    ${UTILS_DIR}/BackendBinding.h
    ${UTILS_DIR}/DawnHelpers.cpp
    ${UTILS_DIR}/DawnHelpers.h
    ${UTILS_DIR}/SystemUtils.cpp
    ${UTILS_DIR}/SystemUtils.h
    ${UTILS_DIR}/TerribleCommandBuffer.cpp
    ${UTILS_DIR}/TerribleCommandBuffer.h
)

list(APPEND UTILS_DEPS
    dawn_common
    libdawn_native
    shaderc_shared
    libdawn
    glfw
)

set(UTILS_INCLUDE_DIRS)

if (DAWN_ENABLE_D3D12)
    list(APPEND UTILS_SOURCES
        ${UTILS_DIR}/D3D12Binding.cpp
    )
endif()

if (DAWN_ENABLE_METAL)
    list(APPEND UTILS_SOURCES
        ${UTILS_DIR}/MetalBinding.mm
    )
    list(APPEND UTILS_DEPS
        "-framework Metal"
        "-framework QuartzCore"
    )
endif()

if (DAWN_ENABLE_NULL)
    list(APPEND UTILS_SOURCES
        ${UTILS_DIR}/NullBinding.cpp
    )
endif()

if (DAWN_ENABLE_OPENGL)
    list(APPEND UTILS_SOURCES
        ${UTILS_DIR}/OpenGLBinding.cpp
    )
    list(APPEND UTILS_DEPS
        glad
    )
endif()

if (DAWN_ENABLE_VULKAN)
    list(APPEND UTILS_SOURCES
        ${UTILS_DIR}/VulkanBinding.cpp
    )
    list(APPEND UTILS_INCLUDE_DIRS
        ${VULKAN_HEADERS_INCLUDE_DIR}
    )
endif()

add_library(utils STATIC ${UTILS_SOURCES})
target_link_libraries(utils ${UTILS_DEPS})
target_include_directories(utils PRIVATE ${UTILS_INCLUDE_DIRS})
DawnInternalTarget("" utils)
if(NOT MSVC)
    # allow C-style casts -- for shaderc
    set_property(TARGET utils APPEND PROPERTY COMPILE_OPTIONS "-Wno-old-style-cast")
endif()
