# Copyright 2022-2023 The Khronos Group Inc.
# Copyright 2022-2023 RasterGrid Kft.
# SPDX-License-Identifier: Apache-2.0


add_executable(ktxtools
        command.cpp
        command.h
        command_compare.cpp
        command_create.cpp
        command_deflate.cpp
        command_encode.cpp
        command_extract.cpp
        command_help.cpp
        command_info.cpp
        command_transcode.cpp
        command_validate.cpp
        deflate_utils.h
        encode_utils_astc.h
        encode_utils_basis.h
        encode_utils_common.h
        format_descriptor.h
        formats.h
        fragment_uri.h
        ktx_main.cpp
        metrics_utils.h
        transcode_utils.cpp
        transcode_utils.h
        utility.h
        validate.cpp
        validate.h
        validation_messages.h
)

create_version_header(tools/ktx ktxtools)

set_target_properties(
    ktxtools
    PROPERTIES
        OUTPUT_NAME ktx
        PDB_NAME ktxtools # Avoid conflict with libktx's .pdb.
)

target_include_directories(
    ktxtools
PRIVATE
    .
    $<TARGET_PROPERTY:imageio,INCLUDE_DIRECTORIES>
    $<TARGET_PROPERTY:ktx,INCLUDE_DIRECTORIES>
)

target_include_directories(
    ktxtools
    SYSTEM
PRIVATE
    ${PROJECT_SOURCE_DIR}/lib
    ${PROJECT_SOURCE_DIR}/other_include
)

target_link_libraries(
    ktxtools
PRIVATE
    imageio
    objUtil
    ktx
    ${ASTCENC_LIB_TARGET}
    $<IF:$<BOOL:${WIN32}>,Pathcch,> # For PathCchRemoveFileSpec on Windows
    fmt::fmt
    cxxopts::cxxopts
)

target_compile_definitions(
    ktxtools
PRIVATE
    $<TARGET_PROPERTY:ktx,INTERFACE_COMPILE_DEFINITIONS>
)

target_compile_features(
    ktxtools
PUBLIC
    cxx_std_17
)

if (KTX_DEVELOPER_FEATURE_PATCH)
    target_sources(
        ktxtools
    PRIVATE
        command_patch.cpp
    )
    target_compile_definitions(
        ktxtools
    PRIVATE
        KTX_DEVELOPER_FEATURE_PATCH=1
    )
endif()

set_tool_properties(ktxtools)
set_code_sign(ktxtools)
