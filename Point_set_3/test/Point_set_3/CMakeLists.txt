# Created by the script cgal_create_CMakeLists
# This is the CMake script for compiling a set of CGAL applications.

cmake_minimum_required(VERSION 3.12...3.29)
project(Point_set_3_Tests)

# CGAL and its components
find_package(CGAL REQUIRED)

create_single_source_cgal_program("point_set_test.cpp")
create_single_source_cgal_program("point_set_test_join.cpp")
create_single_source_cgal_program("test_deprecated_io_ps.cpp")
create_single_source_cgal_program("issue7996.cpp")

#Use LAS
#disable if MSVC 2017
if(NOT MSVC_VERSION OR MSVC_VERSION GREATER_EQUAL 1919 OR MSVC_VERSION LESS 1910)
  find_package(LASLIB QUIET)
  include(CGAL_LASLIB_support)
  if (TARGET CGAL::LASLIB_support)
    target_link_libraries(test_deprecated_io_ps PRIVATE CGAL::LASLIB_support)
  else()
    message(STATUS "NOTICE: the LAS reader test requires LASlib, and will not be compiled.")
  endif()
else()
  message(STATUS "NOTICE: the LAS reader does not work with your version of Visual Studio 2017.")
endif()
