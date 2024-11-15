CI（持续集成，Continuous Integration）是一种软件开发实践，旨在让开发团队能够更快地发现和修复问题，从而提高软件质量和开发效率。以下是 CI 的详细介绍和它在项目中的作用：

### 1. **CI 的概念**

- **持续集成**是一种开发实践，指的是团队成员将他们的代码频繁地（通常是每天多次）集成到共享的代码库中。每次代码集成后，系统会自动构建项目，并运行一系列自动化测试，以确保新代码不会破坏现有功能。
- CI 的目标是尽早发现代码错误，让问题在开发阶段就能被修复，而不是在部署或生产环境中暴露。

### 2. **CI 的工作流程**

一个典型的 CI 流程包含以下步骤：

- **代码提交**：开发者将代码提交到版本控制系统（如 Git）中的项目仓库。
- **自动触发构建**：CI 系统（如 GitHub Actions、Jenkins、GitLab CI/CD 等）检测到代码提交后，会自动触发构建过程。
- **运行测试**：构建完成后，CI 系统会自动运行预设的自动化测试，检查新提交的代码是否引入了错误。
- **生成反馈**：CI 系统将测试结果生成报告，反馈给开发者。如果测试通过，代码可以合并到主分支；如果测试失败，开发者需要修复问题并重新提交代码。

### 3. **CI 的重要性**

- **提高代码质量**：通过自动化测试，可以及时发现问题，确保代码质量。
- **减少集成难度**：频繁集成使得每次提交的代码量变少，降低了集成的复杂性。
- **加快开发速度**：CI 能让开发人员快速得到反馈，不需要等待人工测试，极大提高了开发效率。
- **增强协作**：CI 能够让团队成员在同一个仓库中高效协作，确保代码库始终处于可用的状态。

### 4. **CI 在这个项目中的应用**

在此项目中，CI 主要用于确保：

- **代码的构建和运行稳定性**：每次代码更改都会自动构建，验证代码是否可以成功运行。
- **自动化测试**：每次提交后，CI 系统会运行你实现的单元测试，确保新代码没有破坏现有功能。
- **团队协作的可靠性**：由于是多人协作的项目，CI 能确保每个成员提交的代码不会引入新的错误，从而保证项目的整体质量。

### 5. **如何设置 CI**

在 GitHub 上，你可以使用 **GitHub Actions** 设置 CI。以下是一个简单的 YAML 配置文件示例，通常命名为 `.github/workflows/ci.yml`，放置在项目的 GitHub 仓库中：

```yaml
name: C++ CI

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
      uses: actions/checkout@v2

    - name: Set up C++ build environment
      run: sudo apt-get install -y g++ cmake

    - name: Configure CMake
      run: cmake -S . -B build

    - name: Build project
      run: cmake --build build

    - name: Run tests
      run: ./build/tests  # 假设你有一个叫 tests 的测试可执行文件
```

这个示例配置的 CI 流程包含以下步骤：

- 检测代码变动（当推送到 `main` 分支或提交 Pull Request 时触发）。
- 设置 C++ 环境并构建项目。
- 构建后自动运行测试，检查代码的正确性。

### 6. **总结**

CI 是一个自动化过程，帮助开发团队在代码库中频繁集成并自动测试每次提交的代码。它能够提高代码质量、缩短开发周期，尤其适合多人协作的项目。





# Our Project

```yml
name: Group 404 C++ CI with CMake and Googletest

on:
  push:
    branches:
      - feature-googletest
  pull_request:
    branches:
      - feature-googletest

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
      uses: actions/checkout@v2

    - name: Install dependencies (apt / cmake / gcc)
      run: sudo apt-get update && sudo apt-get install -y cmake g++

    - name: Configure CMake
      run: cmake -S . -B build

    - name: Build project
      run: cmake --build build

    - name: Run tests
      run: |
        cd build
        ctest --output-on-failure

```

这个 GitHub Actions 配置文件用于自动化 C++ 项目的构建和测试流程。工作流的名称为“Group 404 C++ CI with CMake and Googletest”，会在推送到 `feature-googletest` 分支或提交到该分支的 Pull Request 时自动触发。以下是详细解释：

### 文件结构与触发条件

```
yaml


复制代码
name: Group 404 C++ CI with CMake and Googletest
```

- **name**：为这个 GitHub Actions 工作流命名。在 GitHub Actions 界面中可以看到这个名称，便于区分不同的工作流。

```
yaml


复制代码
on:
  push:
    branches:
      - feature-googletest
  pull_request:
    branches:
      - feature-googletest
```

- on

  ：定义工作流的触发条件。

  - **push**：当代码被推送到 `feature-googletest` 分支时触发工作流。
  - **pull_request**：当有人对 `feature-googletest` 分支发起 Pull Request 时触发工作流。

### 工作流的具体任务

工作流中的任务被定义为一个 `jobs` 节点，包含 `build` 任务。

```
yaml


复制代码
jobs:
  build:
    runs-on: ubuntu-latest
```

- **jobs**：定义了 CI 流程中的具体任务。
- **build**：这是任务的名称，负责构建和测试项目。
- **runs-on**：指定任务在 `ubuntu-latest` 虚拟环境中运行（即 Ubuntu 操作系统）。

### 任务步骤（steps）

各步骤按顺序执行，完成代码检出、依赖安装、项目配置、编译和测试：

```
yaml


复制代码
steps:
    - name: Checkout repository
      uses: actions/checkout@v2
```

- **Checkout repository**：使用 `actions/checkout@v2` 从 GitHub 检出项目的代码库到工作流环境中，以便后续步骤可以访问代码文件。

```
yaml


复制代码
    - name: Install dependencies (apt / cmake / gcc)
      run: sudo apt-get update && sudo apt-get install -y cmake g++
```

- **Install dependencies**：安装所需的依赖项，包括 `cmake` 和 `g++`。这一步使用 Ubuntu 的包管理器 `apt` 更新软件包列表，并安装 CMake 和 G++（用于构建 C++ 项目）。

```
yaml


复制代码
    - name: Configure CMake
      run: cmake -S . -B build
```

- Configure CMake

  ：在项目中使用 CMake 进行配置。

  - `-S .`：指定当前目录（项目的根目录）为源代码目录。
  - `-B build`：指定 CMake 在 `build` 目录中生成构建文件（如 Makefile）。这一过程会读取 `CMakeLists.txt`，根据配置生成适合该环境的构建文件。

```
yaml


复制代码
    - name: Build project
      run: cmake --build build
```

- **Build project**：使用 CMake 构建项目。`cmake --build build` 会在 `build` 目录中编译代码并生成可执行文件和测试文件。

```
yaml


复制代码
    - name: Run tests
      run: |
        cd build
        ctest --output-on-failure
```

- Run tests

  ：进入 

  ```
  build
  ```

   目录并运行测试。

  - `cd build`：切换到 `build` 目录，这是构建文件和可执行文件所在的目录。
  - `ctest --output-on-failure`：运行 CTest 命令，该命令会自动执行 `add_test` 中定义的测试。`--output-on-failure` 参数使得在测试失败时输出详细的错误信息，便于调试。

### 总结

这个工作流会自动完成以下步骤：

1. 在 `feature-googletest` 分支推送或创建 PR 时触发。
2. 使用 Ubuntu 作为虚拟环境，检出代码并安装依赖。
3. 使用 CMake 配置项目，并在 `build` 目录中构建项目。
4. 运行测试并输出结果。如果测试失败，将输出详细的失败信息。

这个 CI 配置确保每次代码推送或 PR 创建时，都自动进行构建和测试，帮助开发者及时发现代码问题。





# 链接🔗



[菜鸟教程](https://www.runoob.com/w3cnote/yaml-intro.html)

https://github.com/readme/guides/sothebys-github-actions

https://github.blog/enterprise-software/ci-cd/build-ci-cd-pipeline-github-actions-four-steps/