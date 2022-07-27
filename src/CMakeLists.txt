# The Flutter tooling requires that developers have CMake 3.10 or later
# installed. You should not increase this version, as doing so will cause
# the plugin to fail to compile for some customers of the plugin.
cmake_minimum_required(VERSION 3.10)

project(stockfish_for_flutter_library VERSION 0.0.1 LANGUAGES CXX)


file(GLOB_RECURSE cppPaths "./stockfish/*.cpp")

add_library(stockfish_for_flutter SHARED
  "stockfish_for_flutter.cpp"
  ${cppPaths}
)

set_target_properties(stockfish_for_flutter PROPERTIES
  PUBLIC_HEADER stockfish_for_flutter.h
  OUTPUT_NAME "stockfish_for_flutter"
)

set_property(TARGET stockfish_for_flutter PROPERTY CXX_STANDARD 17)

target_compile_definitions(stockfish_for_flutter PUBLIC DART_SHARED_LIB)

set(NNUE_NAME "nn-ad9b42354671.nnue")
file(DOWNLOAD https://tests.stockfishchess.org/api/nn/${NNUE_NAME} ${CMAKE_BINARY_DIR}/${NNUE_NAME})