[TOC]
## Complier&&IDE
### 一 主流编译器特性支持（C++23）
| 编译器 | 安装命令/获取方式              | C++23 支持状态                     | 特色优势                          |
|--------|--------------------------------|-----------------------------------|----------------------------------|
| GCC 13+| `sudo apt install gcc-13 g++-13` | 支持 20+部分特性, <format> 等     | 跨平台性好，标准支持激进          |
| Clang 16+| `sudo apt install clang-16`   | 支持 std::print, <mdspan>         | 错误信息最友好，编译速度快        |
| MSVC 2022| Visual Studio Installer       | 支持 import std; 部分 range 特性  | Windows 深度集成，调试体验最佳    |

**多编译器测试建议**：
```bash
# CMakeLists.txt 中配置
set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_EXTENSIONS OFF)

# 命令行编译测试
cmake -B build -G Ninja -DCMAKE_CXX_COMPILER=clang++-16
cmake -B build -G "Visual Studio 17 2022" -A x64
```
### 二 IDE 与编辑器推荐
1. **Visual Studio 2022 (Windows)**
   - 必装插件：--> 个人选择
     - ReSharper C++（代码分析/重构）
     - VAX（HLSL 支持）
     - Test Adapter（Google Test 集成）

2. **CLion (跨平台)**
   - 优势：深度整合 CMake，内置内存分析器
   - 技巧：开启 `Settings | Build | CMake | C++20 Modules` 支持

3. **Qt Creator (跨平台)**
   - 特别适合嵌入式开发：内置 QML 调试器，Perf 分析支持

### 三 提升开发效率的插件
| 插件类型       | 推荐工具                      | 功能亮点                          |
|---------------|-----------------------------|----------------------------------|
| **静态分析**  | Clang-Tidy                  | 实时代码检查，支持自定义规则       |
|               | Cppcheck                    | 低误报率的内存错误检测            |
| **代码导航**  | Ctags / GNU Global          | 快速跳转到定义                    |
|               | Doxygen Graphviz            | 可视化类关系图                    |
| **性能分析**  | Hotspot (Linux)             | perf 数据可视化                   |
|               | vTune (Intel)               | CPU 微架构级分析                  |
| **内存调试**  | Dr. Memory (跨平台)         | 比 Valgrind 更快的内存检查        |
|               | heaptrack (Linux)           | 堆内存分配可视化                  |
***
## 工具链
### 一 常用工具链以及包管理
1. 常用工具
| 工具          | 用途                     | 安装方式                     |
|---------------|--------------------------|------------------------------|
| clang-tidy    | 静态分析                 | `apt install clang-tidy`     |
| include-what-you-use | 头文件检查       | `apt install iwyu`           |
| cppcheck      | 代码质量检查             | `apt install cppcheck`       |
| gdb/lldb      | 调试器                   | `apt install gdb lldb`       |
| valgrind      | 内存分析                 | `apt install valgrind`       |

#### 2. 构建与包管理
| 工具 | 类型 | 特点 |
|------|------|------|
| **[CMake](https://cmake.org/)** | 构建系统 | 现代项目标配 |
| **[vcpkg](https://vcpkg.io/)** | 包管理 | Microsoft 开源，1500+ 库 |
| **[Conan](https://conan.io/)** | 包管理 | 去中心化，支持自定义仓库 |
| **[xmake](https://xmake.io/)** | 构建系统 | 国内ruki开发，简单高效 |


### 二 CMake
### 1. 预编译头文件 (CMake):
```cmake
target_precompile_headers(my_target PUBLIC
  <vector>
  <memory>
  <string>
)
```
### 3. AddressSanitizer 启用:
```cmake
target_compile_options(my_target PRIVATE -fsanitize=address,undefined)
target_link_options(my_target PRIVATE -fsanitize=address,undefined)
```

### 4. CMake集成Google Test
```cmake
cmake_minimum_required(VERSION 3.14)
project(MyProject)

# 自动下载GoogleTest
include(FetchContent)
FetchContent_Declare(
  googletest
  GIT_REPOSITORY https://github.com/google/googletest.git
  GIT_TAG release-1.14.0
)
FetchContent_MakeAvailable(googletest)

# 创建测试可执行文件
add_executable(tests
  test/main_test.cpp
)

# 链接GTest
target_link_libraries(tests
  PRIVATE
  gtest_main
  gmock
)

# 添加测试
include(GoogleTest)
gtest_discover_tests(tests)
```
测试示例 (`test/main_test.cpp`):
```cpp
#include <gtest/gtest.h>

TEST(MathTest, Addition) {
  EXPECT_EQ(2 + 2, 4);
}

int main(int argc, char** argv) {
  ::testing::InitGoogleTest(&argc, argv);
  return RUN_ALL_TESTS();
}
```
***
### 三 .clang-format
**Google风格配置**:
```yaml
BasedOnStyle: Google
AllowShortFunctionsOnASingleLine: Inline
ColumnLimit: 120
DerivePointerAlignment: false
PointerAlignment: Left
```
**Microsoft风格配置**:
```yaml
BasedOnStyle: Microsoft
AccessModifierOffset: -4
ColumnLimit: 120
Cpp11BracedListStyle: true
```
使用方式：
```bash
# 格式化单个文件
clang-format -i main.cpp

# 格式化整个项目
find . -name '*.cpp' -o -name '*.h' | xargs clang-format -i

find /path/to/source_dir -maxdepth 1 -type f \( -name "*.cpp" -o -name "*.h" \) -exec clang-format -i {} \;
```
- 使用此命令可以更快捷的格式化文件
### 四 Google Benchmark集成
CMake配置:
```cmake
FetchContent_Declare(
  benchmark
  GIT_REPOSITORY https://github.com/google/benchmark.git
  GIT_TAG v1.8.0
)
FetchContent_MakeAvailable(benchmark)

add_executable(benchmarks bench/main.cpp)
target_link_libraries(benchmarks PRIVATE benchmark::benchmark)
```

基准测试示例:
```cpp
#include <benchmark/benchmark.h>

static void BM_StringCreation(benchmark::State& state) {
  for (auto _ : state) {
    std::string empty_string;
  }
}
BENCHMARK(BM_StringCreation);

static void BM_StringCopy(benchmark::State& state) {
  std::string x = "hello";
  for (auto _ : state) {
    std::string copy(x);
  }
}
BENCHMARK(BM_StringCopy);

BENCHMARK_MAIN();
```
### 五 .clangd配置 (支持C++20+)
`.clangd` 配置文件:
```yaml
CompileFlags:
  Add: 
    - -std=c++23
    - --include-directory=${workspaceFolder}/include
  Remove: -W*
Diagnostics:
  ClangTidy:
    Checks: >
      *,
      -llvm-header-guard,
      -google-readability-todo
Index:
  Background: Build
```
- 我自己使用的
```cpp
CompileFlags:
  Add:
    - "-std=c++2b"        # 使用 C++23 标准
    - "-stdlib=libstdc++" # 使用 GCC 标准库
    - "-I/usr/include/c++/14.20"
    - "-I/usr/include/x86_64-linux-gnu/c++/14.20"
    - "-Iinclude"
    - "-Ithird_party/include"
    # 移除 "-fsyntax-only" 和 "-std=c20"

  # 强制覆盖所有文件的标志（覆盖 compile_commands.json）
  CompilationDatabase: true
```
### 六 项目脚手架推荐
1. **现代 CMake 模板**：
   ```bash
   git clone https://github.com/friendlyanon/cmake-init
   cmake-init --project myproj
   ```

2. **Conan 包管理示例**：
   ```python
   # conanfile.py
   class MyProject(ConanFile):
       settings = "os", "compiler", "build_type", "arch"
       requires = "fmt/10.1.0", "range-v3/0.12.0"
       generators = "CMakeDeps"
   ```

3. **VSCode 开发容器模板**(非必要)：
   ```json
   // .devcontainer/devcontainer.json
   {
     "image": "mcr.microsoft.com/devcontainers/cpp:ubuntu-22.04",
     "customizations": {
       "vscode": {
         "extensions": ["ms-vscode.cpptools-extension-pack"]
       }
     }
   }
   ```
### 八 VSCode配置建议(非必要)
`settings.json`:
```json
{
  "clangd.arguments": [
    "--background-index",
    "--clang-tidy",
    "--header-insertion=iwyu"
  ],
  "C_Cpp.default.cppStandard": "c++23",
  "editor.formatOnSave": true
}
```

## 三 额外开发建议
### 一 modern cpp-优化
1.  C++ Modules 工作流
   ```cmake
   # CMake 3.28+ 支持
   set(CMAKE_EXPERIMENTAL_CXX_MODULE_DYNDEP 1)
   set(CMAKE_EXPERIMENTAL_CXX_SCANDEP_SOURCE "")
   
   add_executable(myapp)
   target_sources(myapp
     PUBLIC FILE_SET all_my_modules TYPE CXX_MODULES FILES
       src/module1.ixx
       src/module2.ixx
   )
   ```

2. **编译缓存加速**
   ```bash
   # 使用 ccache 加速重复编译
   sudo apt install ccache
   cmake -B build -DCMAKE_CXX_COMPILER_LAUNCHER=ccache
   ```

3. **预编译头优化**
   ```cmake
   # 现代 CMake PCH 配置
   target_precompile_headers(my_target PUBLIC
     <vector>
     <memory>
     <string>
     "common.h"
   )
   ```

4. **Docker 开发环境**
   ```Dockerfile
   FROM ubuntu:22.04
   RUN apt update && apt install -y clang-16 cmake ninja-build
   COPY . /app
   WORKDIR /app/build
   RUN cmake -G Ninja -DCMAKE_CXX_COMPILER=clang++-16 ..
   ```
5. 代码质量工具
| 工具 | 用途 | 集成方式 |
|------|------|----------|
| **[clang-tidy](https://clang.llvm.org/extra/clang-tidy/)** | 静态分析 | VS Code/CLion 内置 |
| **[Cppcheck](https://cppcheck.sourceforge.io/)** | 静态分析 | 低误报率内存检查 |
| **[include-what-you-use](https://include-what-you-use.org/)** | 头文件优化 | 减少编译依赖 |
| **[pre-commit](https://pre-commit.com/)** | Git 钩子管理 | 提交前自动检查 |

6. 性能分析工具
| 工具 | 平台 | 特点 |
|------|------|------|
| **[Tracy](https://github.com/wolfpld/tracy)** | 跨平台 | 实时性能分析 |
| **[Hotspot](https://github.com/KDAB/hotspot)** | Linux | perf 数据可视化 |
| **[vTune](https://www.intel.com/content/www/us/en/developer/tools/oneapi/vtune-profiler.html)** | 跨平台 | Intel 深度性能分析 |
| **[heaptrack](https://github.com/KDE/heaptrack)** | Linux | 堆内存分配分析 |

### 二 调试
1. **反向调试 (rr)**
   ```bash
   rr record ./myapp   # 记录执行
   rr replay           # 反向执行调试
   ```

2. **实时内存分析**
   ```cpp
   // 在代码中嵌入检查点
   #include <mcheck.h>
   int main() {
     mcheck_pedantic(NULL);  // 开启内存追踪
     // ... your code ...
     mcheck_check_all();     // 主动检查泄漏
   }
   ```

3. **崩溃自动分析**
   ```bash
   # 生成 core dump
   ulimit -c unlimited
   ./crash_app
   
   # 使用 gdb 分析
   gdb ./crash_app core -ex "bt full" -ex quit
   ```
### 三 性能优化套件
| 工具组合        | 适用场景                  | 命令示例                     |
|----------------|-------------------------|----------------------------|
| Perf + FlameGraph | CPU 热点分析          | `perf record -g; ./flamegraph.pl > out.svg` |
| heaptrack + hotspot | 内存分配优化    | `heaptrack ./myapp; hotspot heaptrack.myapp.*.gz` |
| gbench + compare.py | 版本间性能对比    | `benchmark/tools/compare.py benchmarks master.json new.json` |
## 现代cpp库
#### 1. 基础工具库
| 库名 | 用途 | 特点 | 安装方式 |
|------|------|------|----------|
| **[fmt](https://github.com/fmtlib/fmt)** | 格式化输出 | 替代 `iostream`，性能提升 5-10 倍 | `vcpkg install fmt` |
| **[range-v3](https://github.com/ericniebler/range-v3)** | 范围操作 | C++20 Ranges 的参考实现 | `conan install range-v3/0.12.0` |
| **[abseil](https://github.com/abseil/abseil-cpp)** | Google 基础库 | 提供容器、字符串等高性能组件 | `git clone --depth=1` |
| **[Boost](https://www.boost.org/)** | 全能工具箱 | ASIO/Beast 等明星子库 | `sudo apt install libboost-all-dev` |

#### 2. 并发与异步
| 库名 | 特点 | 适用场景 |
|------|------|----------|
| **[libunifex](https://github.com/facebookexperimental/libunifex)** | C++23 执行器实现 | 异步编程的未来标准 |
| **[Folly](https://github.com/facebook/folly)** | Facebook 高性能库 | 并发数据结构、异步IO |
| **[HPX](https://github.com/STEllAR-GROUP/hpx)** | 并行计算框架 | 科学计算、分布式系统 |

#### 3. 网络与序列化
| 库名 | 协议 | 特点 |
|------|------|------|
| **[Boost.Beast](https://www.boost.org/doc/libs/develop/libs/beast/)** | HTTP/WebSocket | 基于 Asio 的高性能实现 |
| **[cpp-httplib](https://github.com/yhirose/cpp-httplib)** | HTTP 客户端/服务端 | 单头文件，零依赖 |
| **[protobuf](https://protobuf.dev/)** | 二进制序列化 | 跨语言高效数据交换 |
| **[msgpack-c](https://github.com/msgpack/msgpack-c)** | 二进制序列化 | 比 JSON 更小更快 |

#### 4. GUI 与可视化
| 库名 | 平台 | 特点 |
|------|------|------|
| **[Dear ImGui](https://github.com/ocornut/imgui)** | 跨平台 | 即时模式 GUI，开发工具神器 |
| **[SDL2](https://www.libsdl.org/)** | 跨平台 | 游戏/多媒体开发基础 |
| **[Magnum](https://magnum.graphics/)** | 跨平台 | 现代 OpenGL 封装 |

#### 5. 数学与科学计算
| 库名 | 领域 | 特点 |
|------|------|------|
| **[Eigen](https://eigen.tuxfamily.org/)** | 线性代数 | 表达式模板，极致优化 |
| **[OpenCV](https://opencv.org/)** | 计算机视觉 | 工业级图像处理 |
| **[Dlib](http://dlib.net/)** | 机器学习 | 人脸识别、图像处理 |
