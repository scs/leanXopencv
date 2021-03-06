# ----------------------------------------------------------------------------
#  CMake file for cxcoretest. See root CMakeLists.txt
#
# ----------------------------------------------------------------------------
project(mltest)

file(GLOB test_srcs "src/*.cpp")
source_group("Src" FILES ${test_srcs})
file(GLOB test_hdrs "src/*.h*")
source_group("Include" FILES ${test_hdrs})

if(WIN32 AND MSVC)
    set(pch_header "mltest.h")
    set(pch_src "precomp.cpp")
    list(REMOVE_ITEM test_srcs ${CMAKE_CURRENT_SOURCE_DIR}/src/${pch_src})
    set(test_srcs ${CMAKE_CURRENT_SOURCE_DIR}/src/${pch_src} ${test_srcs})
    foreach(src_file ${test_srcs})
        if(${src_file} MATCHES ${pch_src})
            set_source_files_properties(
                ${src_file}
                PROPERTIES
                COMPILE_FLAGS "/Yc${pch_header}"
                )
        else()
            set_source_files_properties(
                ${src_file}
                PROPERTIES
                COMPILE_FLAGS "/Yu${pch_header}"
                )
        endif()
    endforeach()
endif()

include_directories(../cxts)

set(the_target "mltest")

add_executable(${the_target} ${test_srcs} ${test_hdrs})

# Additional target properties
set_target_properties(${the_target} PROPERTIES
	DEBUG_POSTFIX "${OPENCV_DEBUG_POSTFIX}"
	RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin/"
	)


add_dependencies(${the_target} cxts cxcore ml)

# Add the required libraries for linking:
target_link_libraries(${the_target} ${OPENCV_LINKER_LIBS} cxcore ml cxts)

enable_testing()
get_target_property(LOC ${the_target} LOCATION)
add_test(mltest "${LOC}")

if(WIN32)
install(TARGETS ${the_target} RUNTIME DESTINATION bin COMPONENT main)
endif()
