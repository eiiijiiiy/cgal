# This is the CMake script for compiling this folder.

cmake_minimum_required(VERSION 3.12...3.29)
project(Point_set_processing_3_Tests)

# Find CGAL
find_package(CGAL REQUIRED)

# VisualC++ optimization for applications dealing with large data
if (MSVC)
  if ( CMAKE_SIZEOF_VOID_P EQUAL 4 )
    # Allow Windows 32bit applications to use up to 3GB of RAM
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} /LARGEADDRESSAWARE")
  endif()
  # Prints new compilation options
  message( STATUS "USING DEBUG CXXFLAGS   = '${CMAKE_CXX_FLAGS} ${CMAKE_CXX_FLAGS_DEBUG}'" )
  message( STATUS "USING DEBUG EXEFLAGS   = '${CMAKE_EXE_LINKER_FLAGS} ${CMAKE_EXE_LINKER_FLAGS_DEBUG}'" )
  message( STATUS "USING RELEASE CXXFLAGS = '${CMAKE_CXX_FLAGS} ${CMAKE_CXX_FLAGS_RELEASE}'" )
  message( STATUS "USING RELEASE EXEFLAGS = '${CMAKE_EXE_LINKER_FLAGS} ${CMAKE_EXE_LINKER_FLAGS_RELEASE}'" )
endif()

# Executables that do *not* require Eigen
create_single_source_cgal_program( "read_test.cpp" )
create_single_source_cgal_program( "test_read_write_point_set.cpp" )
create_single_source_cgal_program( "test_deprecated_io_point_set.cpp" )
create_single_source_cgal_program( "read_test_with_different_pmaps.cpp" )
create_single_source_cgal_program( "analysis_test.cpp" )
create_single_source_cgal_program( "remove_outliers_test.cpp" )
create_single_source_cgal_program( "wlop_simplify_and_regularize_test.cpp" )
create_single_source_cgal_program( "bilateral_smoothing_test.cpp" )
create_single_source_cgal_program( "edge_aware_upsample_test.cpp" )
create_single_source_cgal_program( "structuring_test.cpp" )

#Use LAS
#disable if MSVC 2017
if(NOT MSVC_VERSION OR MSVC_VERSION GREATER_EQUAL 1919 OR MSVC_VERSION LESS 1910)
  find_package(LASLIB QUIET)
  include(CGAL_LASLIB_support)
  if (TARGET CGAL::LASLIB_support)
    target_link_libraries(test_read_write_point_set PRIVATE ${CGAL_libs} CGAL::LASLIB_support)
    target_link_libraries(test_deprecated_io_point_set PRIVATE ${CGAL_libs} CGAL::LASLIB_support)
  else()
    message(STATUS "NOTICE: the LAS reader test requires LASlib and will not be compiled.")
  endif()
else()
  message(STATUS "NOTICE: the LAS reader does not work with your version of Visual Studio 2017.")
endif()

# Use Eigen
find_package(Eigen3 3.1.0 QUIET) #(requires 3.1.0 or greater)
include(CGAL_Eigen3_support)
if(TARGET CGAL::Eigen3_support)
  # Executables that require Eigen
  create_single_source_cgal_program( "normal_estimation_test.cpp" )
  target_link_libraries(normal_estimation_test PRIVATE CGAL::Eigen3_support)

  create_single_source_cgal_program("hierarchy_simplification_test.cpp")
  target_link_libraries(hierarchy_simplification_test PRIVATE CGAL::Eigen3_support)

  create_single_source_cgal_program("smoothing_test.cpp")
  target_link_libraries(smoothing_test PRIVATE CGAL::Eigen3_support)

  create_single_source_cgal_program("vcm_plane_test.cpp")
  target_link_libraries(vcm_plane_test PRIVATE CGAL::Eigen3_support)

  create_single_source_cgal_program("vcm_all_test.cpp")
  target_link_libraries(vcm_all_test PRIVATE CGAL::Eigen3_support)

  create_single_source_cgal_program("jet_pointer_as_property_map.cpp")
  target_link_libraries(jet_pointer_as_property_map PRIVATE CGAL::Eigen3_support)

  create_single_source_cgal_program("psp_jet_includes.cpp")
  target_link_libraries(psp_jet_includes PRIVATE CGAL::Eigen3_support)
else()
  message(STATUS "NOTICE: Some tests require Eigen 3.1 (or greater), and will not be compiled.")
endif()

find_package(TBB QUIET)
include(CGAL_TBB_support)
if(TARGET CGAL::TBB_support)
  foreach(
    target
    analysis_test smoothing_test bilateral_smoothing_test
    wlop_simplify_and_regularize_test edge_aware_upsample_test
    normal_estimation_test)
    if(TARGET ${target})
      target_link_libraries(${target} PRIVATE CGAL::TBB_support)
    endif()
  endforeach()
else()
  message(STATUS "NOTICE: Tests are not using TBB.")
endif()
