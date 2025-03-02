cmake_minimum_required(VERSION 3.11)
project(qsim LANGUAGES CXX CUDA)

IF (WIN32)
    set(CMAKE_CXX_FLAGS "/O2 /openmp")
ELSE()
    set(CMAKE_CXX_FLAGS "-O3 -fopenmp")
ENDIF()


if(APPLE)
    set(CMAKE_CXX_STANDARD 14)
    include_directories("/usr/local/include" "/usr/local/opt/llvm/include")
    link_directories("/usr/local/lib" "/usr/local/opt/llvm/lib")
endif()

include(FetchContent)

FetchContent_Declare(
  pybind11
  GIT_REPOSITORY https://github.com/pybind/pybind11
  GIT_TAG v2.2.4
)
FetchContent_GetProperties(pybind11)
if(NOT pybind11_POPULATED)
  FetchContent_Populate(pybind11)
  add_subdirectory(${pybind11_SOURCE_DIR} ${pybind11_BINARY_DIR})
endif()

find_package(PythonLibs 3.6 REQUIRED)
find_package(CUDA REQUIRED)

include_directories(${PYTHON_INCLUDE_DIRS} ${pybind11_SOURCE_DIR}/include)

cuda_add_library(qsim_cuda MODULE pybind_main_cuda.cpp)
set_target_properties(qsim_cuda PROPERTIES
       PREFIX "${PYTHON_MODULE_PREFIX}"
       SUFFIX "${PYTHON_MODULE_EXTENSION}"
)
set_source_files_properties(pybind_main_cuda.cpp PROPERTIES LANGUAGE CUDA)
