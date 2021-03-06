



include_directories(${CMAKE_SOURCE_DIR}/include ${HDF5_INCLUDE_DIRS})

FILE(GLOB headers_highfive "${CMAKE_SOURCE_DIR}/include/highfive/*.hpp")
FILE(GLOB headers_highfive_bits "${CMAKE_SOURCE_DIR}/include/highfive/bits/*.hpp")


function(compile_example exemple_source)

    get_filename_component(example_filename ${exemple_source} NAME)
    string(REPLACE ".cpp" "_bin" example_name ${example_filename})

    set(require_cpp11 FALSE)
    if(${example_filename} MATCHES ".*cpp11.*")
        set(require_cpp11 TRUE)
        if(NOT MSVC)
            add_definitions(-std=c++11)
        endif()
    endif()

    if(${example_filename} MATCHES ".*boost.*")
        if(NOT USE_BOOST)
            return()
        endif()
    endif()

    if(${example_filename} MATCHES ".*parallel_hdf5.*")
        if(NOT HIGHFIVE_PARALLEL_HDF5)
            return()
        endif()
    endif()

    if(require_cpp11)
        if(NOT COMPILER_SUPPORTS_CXX11)
        return()
	endif()
    endif()

    add_executable(${example_name} ${exemple_source} ${headers_highfive} ${headers_highfive_bits})
    target_link_libraries(${example_name} ${HDF5_C_LIBRARIES}  ${MPI_C_LIBRARIES})

endfunction()


file(GLOB list_example "*.cpp")

foreach(example_src ${list_example})
compile_example(${example_src})

endforeach()
