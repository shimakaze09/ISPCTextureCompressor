add_library(ispc_texcomp ispc_texcomp.cpp ispc_texcomp_astc.cpp kernel_ispc.o kernel_ispc_sse2.o kernel_ispc_avx.o kernel_astc_ispc.o kernel_astc_ispc_sse2.o kernel_astc_ispc_avx.o)

set(FUNCTIONS kernel_astc kernel)

foreach(FUNC IN LISTS FUNCTIONS)
add_custom_command(OUTPUT ${FUNC}_ispc.o ${FUNC}_ispc_avx.o ${FUNC}_ispc_sse2.o
    COMMAND ${CMAKE_COMMAND} -E env "PATH=${ISPC_COMPILER_PATH}" ${ISPC_COMPILER} ${ISPC_OPTIONS} -o ${FUNC}_ispc.o -h ${FUNC}_ispc.h ${CMAKE_CURRENT_SOURCE_DIR}/${FUNC}.ispc
    MAIN_DEPENDENCY ${CMAKE_CURRENT_SOURCE_DIR}/${FUNC}.ispc
    COMMENT "Building ${FUNC}"
    )
endforeach(FUNC)

set_target_properties(ispc_texcomp
PROPERTIES PUBLIC_HEADER ispc_texcomp.h
)

target_include_directories(ispc_texcomp PRIVATE ${CMAKE_CURRENT_BINARY_DIR})

install(TARGETS ispc_texcomp
    ARCHIVE DESTINATION ${CMAKE_SYSTEM_NAME}/lib
    PUBLIC_HEADER DESTINATION ${CMAKE_SYSTEM_NAME}/include
)
