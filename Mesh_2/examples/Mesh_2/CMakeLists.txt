cmake_minimum_required(VERSION 3.12...3.29)
project(Mesh_2_Examples)

find_package(CGAL REQUIRED OPTIONAL_COMPONENTS Qt6)

# create a target per cppfile
file(
  GLOB cppfiles
  RELATIVE ${CMAKE_CURRENT_SOURCE_DIR}
  ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp)
foreach(cppfile ${cppfiles})
  create_single_source_cgal_program("${cppfile}")
endforeach()

if(CGAL_Qt6_FOUND)
  target_link_libraries(mesh_with_seeds PRIVATE CGAL::CGAL_Basic_viewer)
  target_link_libraries(mesh_marked_domain PRIVATE CGAL::CGAL_Basic_viewer)
endif()
