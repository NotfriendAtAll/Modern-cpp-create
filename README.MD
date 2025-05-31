[TOC]

## Practical-tips-
- 以下是一些实用的C++开发技巧，涵盖代码格式化、测试框架集成、性能测试和语言服务器配置,帮助新人获得更好，更方便，更现代的开发体验
- 这些配置和技巧能显著提升C++开发效率和代码质量，特别是在大型项目中效果更为明显。根据项目需求调整细节参数，可进一步优化开发体验。
### 现代 C++ 开发工作流
```mermaid
graph LR
    A[编辑] --> B[实时 Lint]
    B --> C[编译]
    C --> D[单元测试]
    D --> E[性能分析]
    E --> F[代码审查]
    
    subgraph IDE/编辑器
    A -->|clangd| B
    end
    
    subgraph CI/CD
    C -->|CTest| D
    D -->|Benchmark| E
    end
```

### 一 主流编译器特性支持（C++23）
###   CMake
### 二 .clang-format
### 三 Google Benchmark集成
### 四 .clangd配置 (支持C++20+)

### 六 常用工具链
### 八 VSCode配置建议(非必要)
***
## 学习资源推荐
1. **编译器资源管理器**：https://godbolt.org/（实时查看汇编输出）
2. **C++ 参考**：https://cppreference.com/（权威文档）
3. **C++ Insights**：https://cppinsights.io/（代码转换可视化）
4. **QuickBench**：https://quick-bench.com（在线基准测试）
6. **视频课程**：
   - [CppCon](https://www.youtube.com/user/CppCon)：年度 C++ 会议录像
   - [Cherno's C++ Series](https://www.youtube.com/c/TheChernoProject)：游戏开发视角

7. **书籍**：
   - 《C++20 - The Complete Guide》 Nicolai Josuttis
   - 《Effective Modern C++》Scott Meyers

8. **社区**：
   - [r/cpp](https://www.reddit.com/r/cpp/)：Reddit C++ 社区
   - [C++ Slack](https://cppalliance.org/slack/)：国际开发者群组

以上工具链组合可构建完整的现代 C++ 开发环境，你自己根据项目需求选择
- **大型跨平台项目**：Clang + CMake + VSCode/CLion + vcpkg
- **Windows 桌面应用**：MSVC + Visual Studio + ReSharper
- **高性能计算**：GCC + perf + heaptrack + Ninja
- **嵌入式开发**：GCC交叉编译 + Qt Creator + OpenOCD
- **新项目启动**：cpp_starter_project + vcpkg + GitHub Actions
- **高性能计算**：HPX + Boost.Compute + Tracy
- **跨平台应用**：Qt6 + CMake + Conan
- **服务端开发**：Boost.Beast + fmt + Catch2
##  贡献指南
- 如果你有任何的问题，欢迎留言，提交 issue以及pr,文档会在您的监督下趋于完善。
