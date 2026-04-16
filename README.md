# NVDLA 开源硬件（Open Source Hardware）1.0

---

## 项目简介

NVIDIA Deep Learning Accelerator（NVDLA）是一套免费且开源的深度学习推理加速器架构，旨在推动深度学习推理加速器设计的标准化。NVDLA 采用模块化架构，具备可扩展、高可配置、便于集成与移植的特点。

项目主页：

<http://nvdla.org/>

## 当前发布说明

当前仓库对应 `nvdlav1` 分支上的发布内容，提供的是 NVDLA 的非可配置（non-configurable）“full-precision”版本。该版本固定为：

- 2048 个 8-bit MAC
- 或 1024 个 16-bit fixed-point / floating-point MAC

该分支定位为稳定维护版本，后续可能继续接收缺陷修复，但不会在这个分支上引入新的 RTL 功能增强。与此同时，`nvdlav1` 会与 `master` 分支保持分化关系；`master` 上的提交可以按需 cherry-pick 进入本分支，但不会整体合并 `master`。

## 在线文档

更完整的官方文档请参考：

- 文档总入口：<http://nvdla.org/contents.html>
- Hardware Architecture：<http://nvdla.org/hwarch.html>
- Integrator's Manual：<http://nvdla.org/integration_guide.html>

本 README 仅提供基础说明，详细集成与构建信息请以官方文档为准。

## 仓库结构

本仓库包含与 NVDLA 硬件发布相关的 RTL、C-model、验证环境以及配套脚本。顶层结构可以按下面的方式理解：

```text
NVDLA/
|- cmod/    C-model 与相关模块实现
|- perf/    性能估算资料
|- spec/    RTL 配置与规格相关文件
|- syn/     综合示例脚本
|- tools/   构建、生成与辅助工具
|- verif/   验证环境与示例 traces
`- vmod/    RTL Verilog 实现
```

### `vmod/`：RTL 主体

`vmod/` 是仓库中最核心的 RTL 代码目录，主要包括：

- `vmod/include/`：RTL 共享头文件或公共定义
- `vmod/nvdla/`：NVDLA 各功能模块的 Verilog 实现，例如 `bdma`、`cacc`、`cbuf`、`cdma`、`cdp`、`cmac`、`csc`、`glb`、`pdp`、`rubik`、`sdp`、`top` 等
- `vmod/rams/`：NVDLA 使用到的 RAM 行为模型与综合相关模型
- `vmod/vlibs/`：通用库、基础单元模型与复用组件
- `vmod/plugins/`：插件或扩展相关内容

### `cmod/`：C-model 与高层行为模型

`cmod/` 用于提供与硬件实现配套的 C-model、功能模块行为模型及其公共基础组件，主要包括：

- `cmod/include/`：公共头文件
- `cmod/hls/`：HLS 相关代码、库与封装
- `cmod/nvdla_core/`：NVDLA 核心模型逻辑
- `cmod/nvdla_top/`：顶层模型封装
- `cmod/nvdla_payload/`：数据载荷与接口相关定义
- 功能模块目录：如 `bdma`、`cacc`、`cbuf`、`cdma`、`cdp`、`cmac`、`csc`、`cvif`、`glb`、`mcif`、`pdp`、`rubik`、`sdp` 等，部分目录下带有 `gen/` 子目录用于生成内容或配套产物

### `verif/`：验证环境

`verif/` 用于基本功能验证、仿真与 trace 回放，主要包括：

- `verif/dut/`：待测设计相关封装
- `verif/sim/`：常规仿真环境
- `verif/sim_vivado/`：面向 Vivado 的仿真环境
- `verif/synth_tb/`：综合测试平台与相关脚本
- `verif/traces/`：示例网络 traces，以及 `traceplayer` 等回放支持内容
- `verif/verilator/`：基于 Verilator 的验证支持

### 其他顶层目录

- `syn/`：NVDLA 的综合示例脚本、约束与模板
- `tools/`：构建系统使用的脚本、配置和 `make` 片段，包含 `tools/bin/`、`tools/etc/`、`tools/make/`
- `spec/`：RTL 配置选项、定义与手册相关内容，包含 `spec/defs/`、`spec/manual/`
- `perf/`：性能估算用资料

说明：如 `.dvt/`、`dvt_build.log` 这类目录或文件通常属于本地 IDE / 构建产物示例，不作为仓库正式源码结构的一部分。

## 构建入口

更完整的环境准备、构建参数和运行方式，请参考官方的 Integrator's Manual。结合当前仓库内容，可以先把入口理解为：

1. 根目录的 `Makefile` 用于生成 `tree.make`，以设置工程名和本地工具链路径。
2. 实际仓库中可见的主构建脚本位于 `tools/bin/tmake`。
3. 原始 README 中给出的命令写法是 `bin/tmake`，但按当前仓库目录来看，对应的实际路径应理解为 `tools/bin/tmake`。

最基础的构建入口可写为：

```bash
tools/bin/tmake
```

补充说明：

- `Makefile` 中的默认工具路径明显偏向 Linux 环境
- `tree.make` 属于本地环境初始化结果，通常需要先生成后再参与后续流程
- 如果需要了解更完整的 build targets、project 选择或仿真方式，应以 `tools/bin/tmake`、`tools/etc/build.config` 和官方文档为准
