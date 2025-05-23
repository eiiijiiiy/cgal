# Created by the script cgal_create_cmake_script.
# This is the CMake script for compiling a CGAL application.

cmake_minimum_required(VERSION 3.12...3.29)
project(Shape_detection_Examples)

find_package(CGAL REQUIRED COMPONENTS Core)

create_single_source_cgal_program("efficient_RANSAC_with_custom_shape.cpp")
create_single_source_cgal_program("efficient_RANSAC_with_parameters.cpp")
create_single_source_cgal_program("efficient_RANSAC_with_point_access.cpp")

find_package(Eigen3 3.1.0 QUIET) # (3.1.0 or greater)
include(CGAL_Eigen3_support)
if(TARGET CGAL::Eigen3_support)
  message(STATUS "Found Eigen 3")

  create_single_source_cgal_program("efficient_RANSAC_basic.cpp")
  create_single_source_cgal_program("efficient_RANSAC_with_callback.cpp")
  create_single_source_cgal_program("region_growing_with_custom_classes.cpp")
  create_single_source_cgal_program("region_growing_lines_on_point_set_2.cpp")
  create_single_source_cgal_program("region_growing_circles_on_point_set_2.cpp")
  create_single_source_cgal_program("region_growing_planes_on_point_set_3.cpp")
  create_single_source_cgal_program("region_growing_spheres_on_point_set_3.cpp")
  create_single_source_cgal_program("region_growing_cylinders_on_point_set_3.cpp")
  create_single_source_cgal_program("region_growing_lines_on_segment_set.cpp")
  create_single_source_cgal_program("region_growing_planes_on_polygon_mesh.cpp")
  add_executable(region_growing_planes_on_polyhedron region_growing_planes_on_polygon_mesh.cpp)
  target_compile_definitions(region_growing_planes_on_polyhedron PRIVATE -DUSE_POLYHEDRON)
  target_link_libraries(region_growing_planes_on_polyhedron PRIVATE CGAL::CGAL CGAL::Eigen3_support)

  foreach(
    target
    efficient_RANSAC_basic
    efficient_RANSAC_with_callback
    region_growing_with_custom_classes
    region_growing_lines_on_point_set_2
    region_growing_circles_on_point_set_2
    region_growing_planes_on_point_set_3
    region_growing_spheres_on_point_set_3
    region_growing_cylinders_on_point_set_3
    region_growing_planes_on_polygon_mesh
    region_growing_lines_on_segment_set)

    target_link_libraries(${target} PRIVATE CGAL::Eigen3_support)
  endforeach()
endif()
