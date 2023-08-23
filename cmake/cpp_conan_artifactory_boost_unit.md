```
Set up a well-organized project structure with one library using CMake best practices. Create directories for source code, headers, and tests. Ensure CMake can find these directories correctly. Software stack : c++20, conan, artifactory, boost unit
```

Setting up a project with CMake requires an understanding of the organization, dependencies, and tooling. Given your requirements, let's break this down step by step.

### 1. Directory Structure
Begin by creating a directory structure:

```plaintext
/my_project
|-- CMakeLists.txt
|-- src/
|   |-- CMakeLists.txt
|   |-- main.cpp
|-- include/
|   |-- MyLib/
|       |-- header.hpp
|-- lib/
|   |-- CMakeLists.txt
|   |-- libcode.cpp
|-- tests/
    |-- CMakeLists.txt
    |-- test_main.cpp
```

### 2. Root `CMakeLists.txt`
This is the main configuration file:

```cmake
cmake_minimum_required(VERSION 3.15)
project(MyProject VERSION 1.0 LANGUAGES CXX)

# Set the C++ standard
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Conan setup
include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
conan_basic_setup(TARGETS)

add_subdirectory(src)
add_subdirectory(lib)
add_subdirectory(tests)
```

### 3. `src/CMakeLists.txt` for the main executable:
```cmake
add_executable(MyProject main.cpp)

target_include_directories(MyProject PRIVATE ${CMAKE_SOURCE_DIR}/include)
target_link_libraries(MyProject PRIVATE MyLibrary CONAN_PKG::boost)
```

### 4. `lib/CMakeLists.txt` for the library:
```cmake
add_library(MyLibrary STATIC libcode.cpp)

target_include_directories(MyLibrary PUBLIC ${CMAKE_SOURCE_DIR}/include)
```

### 5. `tests/CMakeLists.txt` for the test:
```cmake
enable_testing()

add_executable(MyTests test_main.cpp)

target_link_libraries(MyTests PRIVATE MyLibrary CONAN_PKG::boost_unit)
add_test(NAME MyLibraryTests COMMAND MyTests)
```

### 6. Conan and Artifactory
Use Conan to manage the C++ packages:

- Create a `conanfile.txt` at the project root:

```plaintext
[requires]
boost/1.75.0

[generators]
cmake
```

- To set up Artifactory with Conan, you will have to add Artifactory as a remote, for instance:

```bash
conan remote add my_artifactory https://my_artifactory_server/artifactory/api/conan/my_conan_repo
```

- To install the dependencies:

```bash
mkdir build && cd build
conan install .. --build=missing
```

### 7. Building with CMake:

After installing the dependencies, configure and build with CMake:

```bash
cd build
cmake ..
make
```

This configuration sets up a project with a library, main executable, and a unit test suite. The project uses Conan for package management, integrated with Artifactory, and targets C++20. 

Always ensure you have the required tools installed, and your Artifactory instance is reachable and correctly set up.
