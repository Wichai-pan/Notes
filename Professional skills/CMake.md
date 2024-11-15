# 简介

CMake 是一个跨平台的构建工具，用于管理编译和构建 C++ 项目。它不是编译器，而是一个生成工具，可以自动创建适合不同平台的构建文件（如 Makefile 或 Visual Studio 工程文件），简化了构建过程。CMake 可以帮助我们跨平台管理复杂项目的依赖关系、编译选项和编译步骤。

### CMake 的基本工作流程

CMake 通过配置文件 `CMakeLists.txt` 来指定项目的构建规则。CMake 的典型流程如下：

1. **编写 `CMakeLists.txt` 文件**：在项目根目录编写一个或多个 `CMakeLists.txt` 文件，定义项目的构建规则、依赖和编译选项。
2. **运行 CMake 配置项目**：CMake 读取 `CMakeLists.txt` 文件，并生成适合特定编译环境的构建文件（例如，Linux 上的 Makefile 或 Windows 上的 Visual Studio 项目文件）。
3. **构建项目**：通过生成的构建文件调用编译器进行编译。

### 基本的 CMakeLists.txt 结构

我们来看一个简单的 `CMakeLists.txt` 文件示例，帮助你了解 CMake 的基本语法和使用方式。

```
cmake


复制代码
# 指定 CMake 的最低版本要求
cmake_minimum_required(VERSION 3.10)

# 定义项目名称和版本
project(MyProject VERSION 1.0)

# 设置 C++ 标准
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED True)

# 添加源文件并定义可执行文件
add_executable(MyExecutable main.cpp other_file.cpp)

# 查找和链接外部库
find_package(SomeLibrary REQUIRED)
target_link_libraries(MyExecutable PRIVATE SomeLibrary)
```

### CMakeLists.txt 文件的关键指令

1. **`cmake_minimum_required`**：指定所需的 CMake 最低版本。确保项目在该版本或更高版本的 CMake 中能正常运行。
2. **`project`**：定义项目名称和版本。CMake 会自动设置一些与项目相关的变量（如项目名称和版本号）。
3. **`set`**：用于设置变量。这里我们设置了 `CMAKE_CXX_STANDARD` 为 17，表示项目使用 C++17 标准。
4. **`add_executable`**：定义可执行文件。第一个参数是生成的可执行文件名，后面是源代码文件列表（如 `main.cpp` 和其他相关文件）。
5. **`find_package` 和 `target_link_libraries`**：这两个命令用于查找和链接外部库。如果项目依赖某些外部库，`find_package` 会尝试找到该库并设置路径，`target_link_libraries` 则用于将库链接到目标可执行文件。

### CMake 构建流程示例

假设你有一个包含 `main.cpp` 和 `CMakeLists.txt` 的简单项目。可以使用以下命令完成配置和构建：

1. 配置项目

   ：在命令行中进入项目根目录，然后运行以下命令：

   ```
   bash
   
   
   复制代码
   cmake -S . -B build
   ```

   - `-S .` 指定源代码所在的目录（当前目录 `.`）。
   - `-B build` 指定 CMake 将生成的构建文件存放在 `build` 文件夹中。

2. 编译项目

   ：执行以下命令，开始编译：

   ```
   bash
   
   
   复制代码
   cmake --build build
   ```

   这会使用 

   ```
   build
   ```

    文件夹中的构建文件来编译项目，生成可执行文件。

### CMake 的常用指令

- **`include_directories`**：指定头文件路径。例如 `include_directories(include/)` 将 `include` 文件夹添加到搜索路径。
- **`add_library`**：定义库文件，可以是静态库或动态库。
- **`target_include_directories`**：为目标（如可执行文件或库）指定额外的头文件目录。
- **`install`**：指定安装规则，将编译后的文件安装到指定目录。

### 复杂项目的 CMake 配置

对于有多个文件夹或模块的项目，可以使用多个 `CMakeLists.txt` 文件组织代码。在主 `CMakeLists.txt` 中可以用 `add_subdirectory` 添加子项目：

```
cmake


复制代码
# 主 CMakeLists.txt
cmake_minimum_required(VERSION 3.10)
project(ComplexProject)

add_subdirectory(src)
add_subdirectory(tests)
```

在 `src/CMakeLists.txt` 文件中可以进一步定义库或可执行文件：

```
cmake


复制代码
# src/CMakeLists.txt
add_library(MyLibrary my_library.cpp)
add_executable(MyExecutable main.cpp)
target_link_libraries(MyExecutable PRIVATE MyLibrary)
```

### 总结

CMake 是一个强大且灵活的构建工具，特别适合跨平台和大型 C++ 项目。通过 `CMakeLists.txt` 文件，你可以定义项目的构建过程、管理依赖关系、指定编译选项等。在实际开发中，结合 CMake 可以让 CI 流程更顺畅，并确保项目能在不同平台下构建和运行。

# 学习链接🔗

1. https://cmake.org/cmake/help/latest/guide/tutorial/
2. https://github.com/SFUMECJF/cmake-examples-Chinese
3. https://lellansin.gitbooks.io/cmake/content/basic.html