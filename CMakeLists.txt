cmake_minimum_required(VERSION 3.9.4)

# CXX Flags Setup
set(CMAKE_CXX_FLAGS_DEBUG "-g")
set(CMAKE_CXX_FLAGS_MINSIZEREL "-Os -DNDEBUG")
set(CMAKE_CXX_FLAGS_RELWITHDEBINFO "-O2 -g -DNDEBUG")
# set(CMAKE_CXX_FLAGS_RELEASE "-O3 -DNDEBUG")
#set(CMAKE_CXX_FLAGS_RELEASE "-O3 -g -flto")
set(CMAKE_CXX_FLAGS_RELEASE "-O3 -g")

set(CMAKE_BUILD_TYPE "Release")
#set(CMAKE_BUILD_TYPE "Debug")

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -I/usr/local/include" )
set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -L/usr/local/lib")

# C++11
include(CheckCXXCompilerFlag)
CHECK_CXX_COMPILER_FLAG("-std=c++11" COMPILER_SUPPORTS_CXX11)
CHECK_CXX_COMPILER_FLAG("-std=c++0x" COMPILER_SUPPORTS_CXX0X)
if(COMPILER_SUPPORTS_CXX11)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
elseif(COMPILER_SUPPORTS_CXX0X)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++0x")
else()
        message(STATUS "The compiler ${CMAKE_CXX_COMPILER} has no C++11 support. Please use a different C++ compiler.")
endif()

# Klampt CMake Properties
set(KLAMPT_ROOT "/home/motion/Klampt")
set(CMAKE_MODULE_PATH "${KLAMPT_ROOT}/CMakeModules")
find_package(Klampt REQUIRED)
add_definitions(${KLAMPT_DEFINITIONS})
include_directories(${KLAMPT_INCLUDE_DIRS} "/home/motion/Klampt/Cpp" "/home/motion/Klampt/Cpp/Dependencies" "/home/motion/Klampt/Cpp/Dependencies/KrisLibrary")

# SNOPT CMake Properties
set(SNOPT_PATH "/home/motion/snopt7")         # cppsrc to be -I: (-I/home/motion/snopt7/cppsrc/)
include_directories(${SNOPT_PATH}/include)
link_directories(${SNOPT_PATH}/lib)
set(SNOPT_LIBRARIES snopt7_cpp snopt7)

# EIGEN CMake Properties
set(EIGEN3_INCLUDE_DIR "/home/motion/eigen-3.3.5")
include_directories(${EIGEN3_INCLUDE_DIR})

# CILANTRO CMake Properties
set(CILANTRO_PATH "/home/motion/cilantro")
include_directories(${CILANTRO_PATH}/include)
include_directories(/home/motion/Pangolin/include)
include_directories(/home/motion/Pangolin/build/src/include/)
link_directories(${CILANTRO_PATH}/build/)
add_definitions(-DENABLE_NON_DETERMINISTIC_PARALLELISM -DHAVE_PANGOLIN)
set(CILANTRO_LIBRARIES cilantro.so pangolin.so)

# HJB CMake Properties
set(PVK_PATH "/home/motion/Desktop/Polytopic-Viability-Kernel-Plus")
include_directories(${PVK_PATH}/src)

# Gurobi CMake Properties
set(GUROBI_PATH "/home/motion/gurobi811/linux64")
include_directories(${GUROBI_PATH}/include)
link_directories(${GUROBI_PATH}/lib)
set(GUROBI_LIBRARIES gurobi_c++ gurobi81)

# Cddlib CMake Properties
set(Cddlib_LIBRARY cdd)

# Main Code Properties
add_executable(MyApp
               src/Main.cpp
               src/CommonHeader.h
               src/ContactInfoLoader.cpp
               src/SignedDistanceField.cpp
               src/RobotStateFiler.cpp
               src/RobotUtils.cpp
	       src/InitialStateOptimization.cpp
	       src/InitialConfigGene.cpp
	       src/KeyFrameOptimization.cpp
               src/NonlinearOptimizerInfo.h
               src/SimulationTest.cpp
               src/ZeroStepCapturability.cpp
	       src/ViabilityKernelLoader.cpp
               src/HJBController.cpp
	       src/HJBControllerVelo.cpp
	       src/ConvexPolyhedron.cpp
	       src/QPStabilizer.cpp
	       src/StabilizingControllerGRB.cpp
	       src/StabilizingControllerContact.cpp
	       src/CapturePointAnalysis.cpp)

target_link_libraries(MyApp  ${KLAMPT_LIBRARIES} ${SNOPT_LIBRARIES} ${CILANTRO_LIBRARIES} ${GUROBI_LIBRARIES} ${Cddlib_LIBRARY})
#set_property(TARGET MyApp PROPERTY INTERPROCEDURAL_OPTIMIZATION True)
get_property(dirs DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR} PROPERTY INCLUDE_DIRECTORIES)
foreach(dir ${dirs})
  message(STATUS "dir='${dir}'")
endforeach()
