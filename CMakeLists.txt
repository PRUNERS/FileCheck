cmake_minimum_required(VERSION 3.4.3)

set(CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake ${CMAKE_MODULE_PATH})

set(LLVM_ROOT "" CACHE PATH "Root of LLVM install.")
find_package(LLVM MODULE REQUIRED)

# Sanity check
if(NOT EXISTS ${LLVM_ROOT}/include/llvm)
  message(FATAL_ERROR "LLVM_ROOT (${LLVM_ROOT}) is not a valid LLVM install")
endif()

# Incorporate the CMake features provided by LLVM:
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${LLVM_ROOT}/lib/cmake/llvm")

include(LLVMConfig)
include(HandleLLVMOptions)
include(AddLLVM)

add_definitions(${LLVM_DEFINITIONS})
include_directories(${LLVM_INCLUDE_DIRS})

option(LLVM_INCLUDE_UTILS "Generate build targets for the LLVM utils." ON)

set(llvm_lit_path ${CMAKE_BINARY_DIR}/llvm-lit)
configure_file(llvm/utils/llvm-lit/llvm-lit.in ${llvm_lit_path})

add_executable(FileCheck llvm/utils/FileCheck/FileCheck.cpp)
target_link_libraries(FileCheck LLVMSupport)

install(TARGETS FileCheck RUNTIME DESTINATION bin)
install(PROGRAMS ${llvm_lit_path} DESTINATION bin)
