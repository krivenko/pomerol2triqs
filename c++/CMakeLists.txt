add_library(pomerol2triqs_c pomerol_ed.cpp g.cpp g2.cpp chi.cpp)

find_package(Boost COMPONENTS mpi serialization)

target_link_libraries(pomerol2triqs_c PUBLIC triqs  ${pomerol_LIBRARIES})

target_include_directories(pomerol2triqs_c PUBLIC ${pomerol_INCLUDE_DIRS})
target_compile_options(pomerol2triqs_c PRIVATE
  -DPOMEROL2TRIQS_GIT_HASH=${POMEROL2TRIQS_GIT_HASH}
  -DPOMEROL2TRIQS_GIT_HASH=${POMEROL2TRIQS_GIT_HASH}
)

install(TARGETS pomerol2triqs_c DESTINATION lib EXPORT pomerol2triqs-targets)
install(EXPORT pomerol2triqs-targets DESTINATION share/cmake)

install(FILES pomerol_ed.hpp g2_parameters.hpp DESTINATION include/pomerol2triqs)
