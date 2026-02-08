cmake_minimum_required(VERSION 3.10)
project(flashGenomeAligner LANGUAGES CXX)

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
add_executable(flashGenomeAligner src/flashGenomeAligner.cpp)

# Link Libraries
target_link_libraries(flashGenomeAligner 
    PRIVATE 
    OpenMP::OpenMP_CXX 
    parasail
)

# Compiler definitions/options (Optional: optimization flags)
if(MSVC)
    target_compile_options(flashGenomeAligner PRIVATE /O2 /arch:AVX2)
else()
    target_compile_options(flashGenomeAligner PRIVATE -O3 -march=native -flto)
endif()


# Copy DLL to output directory
add_custom_command(TARGET flashGenomeAligner POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_if_different
    "${PARASAIL_LIBRARY_DIR}/parasail.dll"
    $<TARGET_FILE_DIR:flashGenomeAligner>
)

message(STATUS "Project configured. Build with --config Release for maximum performance.")
