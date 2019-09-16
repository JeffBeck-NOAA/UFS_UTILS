cmake_minimum_required(VERSION 2.6)
set(EXENAME  chgres_cube.exe )
file(GLOB EXE_SRC ${CMAKE_CURRENT_SOURCE_DIR}/*.F90 ${CMAKE_CURRENT_SOURCE_DIR}/*.f90 ${CMAKE_CURRENT_SOURCE_DIR}/*.F)
#file(GLOB EXE_C_SRC ${CMAKE_CURRENT_SOURCE_DIR}/*.c)
if(IntelComp)
    message("setting intel flags")
    set(CMAKE_Fortran_4_FLAGS " -g -O3 -axCORE-AVX2" CACHE INTERNAL "" )
else()
    message("unkown compiler!")
    exit()
endif()


#set_source_files_properties( ${EXE_C_SRC} PROPERTIES COMPILE_FLAGS ${CMAKE_C_FLAGS} )
#add_definitions( ${CMAKE_Fortran_4_FLAGS} )
add_executable(${EXENAME} ${EXE_SRC} ${EXE_C_SRC} )
set_target_properties( ${EXENAME} PROPERTIES COMPILE_FLAGS ${CMAKE_Fortran_4_FLAGS} )
set_target_properties( ${EXENAME} PROPERTIES LINK_FLAGS ${CMAKE_Fortran_4_FLAGS} )
message("sfcio lib is ${SFCIO_LIBRARY} ${W3NCO_LIBRARY}")
include_directories( ${G2_INC4} ${ESMF_INC} ${NEMSIOINC} ${NETCDF_INCLUDES} ${MPI_Fortran_INCLUDE_PATH} ${SFCIOINC} ${NETCDF_INCLUDES_F90})
set(CMAKE_INCLUDE_OUTPUT_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/include_4")
set_target_properties(${EXENAME} PROPERTIES Fortran_MODULE_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/include_4")
target_include_directories(${EXENAME} PUBLIC "${CMAKE_CURRENT_BINARY_DIR}/include_4")
target_link_libraries(${EXENAME}  ${NETCDF_LIBRARIES_F90} ${NETCDF_LIBRARIES} ${NEMSIO_LIBRARY} ${ESMF_LIBRARY} ${SFCIO_LIBRARY} ${MPI_Fortran_LIBRARIES} ${SIGIO_LIBRARY} ${BACIO_LIBRARY} ${SP_LIBRARY} ${W3NCO_LIBRARY} ${OpenMP_Fortran_FLAGS} )

file(MAKE_DIRECTORY ${CMAKE_INCLUDE_OUTPUT_DIRECTORY})

install(TARGETS ${EXENAME}
        RUNTIME DESTINATION ${CMAKE_INSTALL_PREFIX}/bin
        LIBRARY DESTINATION ${CMAKE_INSTALL_PREFIX}/lib
        ARCHIVE DESTINATION ${CMAKE_INSTALL_PREFIX}/lib) 
install(DIRECTORY ${CMAKE_INCLUDE_OUTPUT_DIRECTORY}/ DESTINATION include_4 )
