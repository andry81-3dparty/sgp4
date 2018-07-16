cmake_minimum_required(VERSION 3.0)

include(Common)

project("sgp4")

set(PROJECT_LIB_NAME sgp4)

discover_variable(QD_ROOT PATH "QD math library")
discover_variable(SGP4_ENABLE_QD_QD_INTEGRATION STRING "SGP4 library qd_real replacement for the double")
discover_variable(SGP4_ENABLE_QD_DD_INTEGRATION STRING "SGP4 library dd_real replacement for the double")


# Workaround for the issue: https://stackoverflow.com/questions/51272106/cmake-add-subdirectory-twice-as-a-shared-project
#
# The error:
#
#   CMake Error at _3dparty/sat/sgp4/CMakeLists.txt:XX (target_link_libraries):
#     Attempt to add link library "qd" to target "sgp4" which is not built in
#     this directory.
#
file(GLOB_RECURSE all_headers ${CMAKE_CURRENT_LIST_DIR}/libsgp4/*.h*)
file(GLOB_RECURSE all_sources ${CMAKE_CURRENT_LIST_DIR}/libsgp4/*.cpp)

add_library(${PROJECT_LIB_NAME} STATIC ${all_headers};${all_sources})
#add_library(${PROJECT_LIB_NAME}s SHARED ${all_headers};${all_sources})

#add_subdirectory(${CMAKE_CURRENT_LIST_DIR}/libsgp4) # otherwise the error would appear

source_groups_from_dir_list("Header Files" FILES ${CMAKE_CURRENT_LIST_DIR} *.h*)
source_groups_from_dir_list("Source Files" FILES ${CMAKE_CURRENT_LIST_DIR} *.cpp)

#target_include_directories(${PROJECT_LIB_NAME}
#  PUBLIC
#    ${CMAKE_CURRENT_LIST_DIR}/core
#)

foreach(target ${PROJECT_LIB_NAME})#;${PROJECT_LIB_NAME}s)
  add_target_compile_definitions(${target} *
    PUBLIC
      _X86_   # basically defined by MinGW, so define explicitly to compile in MSVC
  )

  if(GCC)
    add_target_compile_definitions(${target} DEBUG
      PUBLIC
        _DEBUG
    )

    add_target_compile_properties(${target} DEBUG
      -g
    )

    add_target_compile_properties(${target} *
      -m32        # compile 32 bit target on 64 bit host
      # from library authors
      -Wall -Wextra -Wshadow -Wshadow -Wnon-virtual-dtor -pedantic -Wno-long-long
      -Wcast-align -Wsign-conversion
    )

    add_target_link_properties(${target} *
      -m32        # link 32 bit target on 64 bit host
    )
  endif()
endforeach()

# integration with QD

add_target_subdirectory(qd ${QD_ROOT} ${CMAKE_BUILD_ROOT}/_3dparty/math/qd)

add_dependencies(${PROJECT_LIB_NAME} qd)

target_link_libraries(${PROJECT_LIB_NAME} # the workaround for the error from here
  PUBLIC
    qd
)

if (SGP4_ENABLE_QD_QD_INTEGRATION)
  add_target_compile_definitions(${PROJECT_LIB_NAME} *
    PUBLIC
      SGP4_ENABLE_QD_QD_INTEGRATION
  )
endif()
if (SGP4_ENABLE_QD_DD_INTEGRATION)
  add_target_compile_definitions(${PROJECT_LIB_NAME} *
    PUBLIC
      SGP4_ENABLE_QD_DD_INTEGRATION
  )
endif()