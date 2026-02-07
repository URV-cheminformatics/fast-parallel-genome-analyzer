cmake_minimum_required(VERSION 3.10)
project(flashGeneAligner LANGUAGES CXX)

# C++ Standard
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Dependencies
find_package(OpenMP REQUIRED)

# Setup External Dependencies (Parasail)
# Adjust these paths if your parasail installation is different
set(PARASAIL_ROOT "${CMAKE_SOURCE_DIR}/external/parasail")
set(PARASAIL_INCLUDE_DIRS "${PARASAIL_ROOT}")
set(PARASAIL_LIBRARY_DIR "${PARASAIL_ROOT}/build")

# Include directories
include_directories(
    ${CMAKE_SOURCE_DIR}/src
    ${PARASAIL_INCLUDE_DIRS}
)

# Link directories
link_directories(${PARASAIL_LIBRARY_DIR})

# Add Executable
add_executable(flashGeneAligner src/flashGeneAligner.cpp)

# Link Libraries
target_link_libraries(flashGeneAligner 
    PRIVATE 
    OpenMP::OpenMP_CXX 
    parasail
)

# Compiler definitions/options (Optional: optimization flags)
if(MSVC)
    target_compile_options(flashGeneAligner PRIVATE /O2 /arch:AVX2)
else()
    target_compile_options(flashGeneAligner PRIVATE -O3 -march=native -flto)
endif()


# Copy DLL to output directory
add_custom_command(TARGET flashGeneAligner POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_if_different
    "${PARASAIL_LIBRARY_DIR}/parasail.dll"
    $<TARGET_FILE_DIR:flashGeneAligner>
)

message(STATUS "Project configured. Build with --config Release for maximum performance.")
