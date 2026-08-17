# 13 信道实时监控可视化工具 (WiFi 攻击日志可视化)

> **一个基于 Python/Tkinter 的串口日志实时可视化工具，专为 WiFi 攻击测试（如 Deauth 攻击）的日志监控与分析设计。**

[![Python](https://img.shields.io/badge/Python-3.7+-3776AB.svg?style=flat&logo=python&logoColor=white)](https://www.python.org/)
[![Tkinter](https://img.shields.io/badge/GUI-Tkinter-0078D4.svg?style=flat&logo=tkinter&logoColor=white)](https://docs.python.org/3/library/tkinter.html)
[![Serial](https://img.shields.io/badge/Serial-pySerial-4B8BBE.svg?style=flat&logo=serial&logoColor=white)](https://pyserial.readthedocs.io/)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![GitHub](https://img.shields.io/badge/GitHub-t84RT-181717.svg?style=flat&logo=github)](https://github.com/t84RT)

---

## 📖 目录

- [项目概述](#项目概述)
- [核心功能](#核心功能)
- [版本演进历程](#版本演进历程)
  - [V1.0 - 基础版 (serial_visualizer.py)](#v10---基础版-serial_visualizerpy)
  - [V2.0 - 速率统计版 (serial_visualizerV2.py)](#v20---速率统计版-serial_visualizerv2py)
  - [V3.0 - 超详细版 (serial_visualizerV3.py)](#v30---超详细版-serial_visualizerv3py)
  - [V5.0 - 作者信息与跑马灯版 (serial_visualizerV5.py)](#v50---作者信息与跑马灯版-serial_visualizerv5py)
  - [V6.0 - 仪表盘与波形图版 (serial_visualizerV6.py)](#v60---仪表盘与波形图版-serial_visualizerv6py)
- [技术架构](#技术架构)
  - [整体架构图](#整体架构图)
  - [模块划分](#模块划分)
  - [数据流设计](#数据流设计)
- [安装与使用](#安装与使用)
  - [环境要求](#环境要求)
  - [安装步骤](#安装步骤)
  - [快速开始](#快速开始)
  - [日志格式说明](#日志格式说明)
- [界面详解](#界面详解)
  - [主界面布局](#主界面布局)
  - [交互操作说明](#交互操作说明)
- [API 参考](#api-参考)
  - [ChannelVisualizer 类](#channelvisualizer-类)
  - [串口操作](#串口操作)
  - [日志解析引擎](#日志解析引擎)
  - [UI 更新引擎](#ui-更新引擎)
  - [绘图引擎](#绘图引擎)
- [性能优化](#性能优化)
- [常见问题](#常见问题)
- [贡献指南](#贡献指南)
- [作者与鸣谢](#作者与鸣谢)
- [许可证](#许可证)

---

<img width="1308" height="866" alt="image" src="https://github.com/user-attachments/assets/53514b33-e360-4980-9479-936981f975a3" />


## 演示视频

### 视频 1：基础功能演示
![基础功能演示](https://github.com/t84RT/13-channel-real-time-monitoring-and-visualization-tool/blob/main/bandicam%202026-08-17%2016-09-15-002.mp4?raw=true)

### 视频 2：速率统计与柱状图
![速率统计与柱状图](https://github.com/t84RT/13-channel-real-time-monitoring-and-visualization-tool/blob/main/bandicam%202026-08-17%2016-21-24-782.mp4?raw=true)

### 视频 3：波形图与仪表盘
![波形图与仪表盘](https://github.com/t84RT/13-channel-real-time-monitoring-and-visualization-tool/blob/main/bandicam%202026-08-17%2016-23-36-733.mp4?raw=true)


## 项目概述

**13 信道实时监控可视化工具** 是一款专为 WiFi 安全测试（尤其是 Deauth 攻击测试）设计的串口日志可视化分析平台。它通过实时读取串口数据，解析特定格式的日志输出，将复杂的文本数据转化为直观的柱状图、动态表格和实时统计信息，帮助安全研究人员和网络管理员快速掌握 13 个 WiFi 信道的攻击态势。

### 项目背景

在 WiFi 渗透测试和网络安全研究中，Deauth（解除认证）攻击是一种常见的测试手段。攻击者通过向目标 AP 发送伪造的解除认证帧，强制客户端断开连接。在此过程中，测试设备（如 ESP8266、ESP32 等）会通过串口输出详细的日志信息，包括：

- 各信道的帧统计（总帧数、单播帧、广播帧）
- 扫描到的 AP 列表及其关联的 STA（客户端）
- 无客户端信道的广播包统计

然而，传统的串口调试工具（如 PuTTY、SecureCRT）只能以文本形式展示这些日志，难以直观地呈现数据间的关系和变化趋势。本工具应运而生，将原始日志转化为可视化的图表和统计面板，极大地提升了数据分析效率。

### 适用场景

| 场景 | 说明 |
|------|------|
| **WiFi 安全测试** | 实时监控 Deauth 攻击效果，分析各信道帧分布 |
| **嵌入式开发调试** | 可视化 ESP8266/ESP32 等设备的串口日志输出 |
| **网络运维监控** | 监控 WiFi 环境中的 AP 和客户端活动 |
| **教学演示** | 直观展示 WiFi 协议帧的统计与可视化 |

### 技术选型

- **Python 3.7+**：跨平台、易于扩展
- **Tkinter**：Python 标准 GUI 库，无需额外安装
- **pySerial**：成熟的串口通信库，支持跨平台
- **正则表达式**：高效解析结构化日志
- **多线程**：串口读取与 UI 渲染分离，保证界面流畅

---

## 核心功能

### 🎯 实时数据采集
- 支持标准串口参数配置（波特率、数据位、停止位、校验位）
- 自动检测并列出可用串口设备
- 多线程异步读取，不阻塞 UI

### 📊 多维度可视化
- **柱状图**：直观展示 13 个信道的帧数/广播数分布
- **实时速率波形图**：动态显示当前信道帧速率变化趋势（V6.0+）
- **详细统计表格**：每个信道的总帧数、单播/广播占比、瞬时速率

### 📋 数据解析与展示
- **当前攻击信道**：实时显示正在攻击的信道及其帧统计
- **AP 列表**：展示扫描到的 AP（SSID、BSSID、信道、关联客户端）
- **无客户端信道广播**：单独列出无客户端信道的广播包数量
- **原始日志**：保留完整的串口输出，便于回溯

### ⚡ 性能与体验
- **预编译正则表达式**：提升日志解析性能（V6.0+）
- **心跳指示灯**：直观显示数据接收状态（V6.0+）
- **跑马灯**：动态展示项目信息与运行状态（V5.0+）
- **实时状态栏**：显示连接状态、接收速率、运行时长（V6.0+）

### 💾 数据管理
- **导出日志**：将原始日志保存为文本文件（V6.0+）
- **清空显示**：一键重置所有数据和图表

---

## 版本演进历程

本项目经历了五个主要版本的迭代，从基础的日志显示逐步演变为功能完善的专业可视化工具。每个版本都代表了不同的设计理念和功能侧重。

### V1.0 - 基础版 (serial_visualizer.py)

**发布时间**：初始版本

**设计目标**：实现串口日志的基本显示与解析

**核心功能**：
- 串口连接与数据读取
- 三类日志解析：
  - 信道完成统计（总帧数、单播、广播）
  - 无客户端广播统计
  - AP 与 STA 扫描结果
- 基础 UI 布局：统计摘要 + AP 列表 + 原始日志

**界面布局**：
```
┌──────────────────────────────────────────────────────┐
│ 串口设置栏（端口 | 波特率 | 打开 | 清空 | 状态）    │
├──────────────────────────┬───────────────────────────┤
│ 左：实时统计             │ 右：AP 列表               │
│  - 当前信道帧统计        │  - CH | SSID | BSSID     │
│  - 无客户端信道广播      │  - 客户端 MAC 列表        │
├──────────────────────────┴───────────────────────────┤
│ 原始日志 (ScrolledText)                              │
└──────────────────────────────────────────────────────┘
```

**数据模型**：
```python
self.current_channel = None      # 当前攻击信道
self.current_total = None        # 当前信道总帧数
self.current_unicast = None      # 单播 (数量, 百分比)
self.current_broadcast = None    # 广播 (数量, 百分比)
self.channel_broadcast = {}      # {信道: 广播包数}
self.ap_list = []                # [{'ch':, 'ssid':, 'bssid':, 'clients': []}]
```

**解析逻辑**：
- 使用 `re.match()` 逐行匹配日志格式
- 采用 `pending_*` 缓存机制处理三行一组的信道统计
- 通过 `last_ap_bssid` 关联 AP 与其 STA

**局限性**：
- 无速率统计
- 无柱状图
- UI 布局较简单

---

### V2.0 - 速率统计版 (serial_visualizerV2.py)

**设计目标**：在 V1.0 基础上增加速率统计和柱状图可视化

**新增功能**：

#### 1. 速率统计模块
```python
self.start_time = None           # 程序启动时间
self.total_frames = 0            # 累计总帧数
self.last_channel_time = {}      # 各信道上次更新时间
self.last_channel_frames = {}    # 各信道上次帧数
self.peak_rate = 0.0             # 峰值速率
self.current_channel_rate = 0.0  # 当前信道瞬时速率
```

速率计算逻辑：
- 每次收到信道完成统计时，计算该信道的瞬时速率
- 更新全局峰值速率
- 在 UI 中显示：运行时间、总帧数、平均速率、瞬时速率、峰值速率

#### 2. 柱状图 (Canvas 绘制)
- 13 个信道以柱状图形式展示
- 柱高表示帧数或广播数
- 当前攻击信道以红色高亮
- 显示纵坐标刻度（0 ~ 最大值）
- 每个柱子上方显示具体数值

#### 3. 界面优化
- 窗口尺寸调整为 1280×850
- 左侧为柱状图，右侧为详细信息
- 新增「速率统计」面板

**数据模型变化**：
```python
self.channel_data = {}  # {ch: {'total':, 'unicast':, 'broadcast':}}
# 替代了原有的分散变量
```

**解析逻辑改进**：
- 采用 `strip()` 处理行数据，提高匹配成功率
- 信道统计完成后直接存入 `channel_data`

**局限性**：
- 仍为单窗口布局
- 无各信道详细统计表格

---

### V3.0 - 超详细版 (serial_visualizerV3.py)

**设计目标**：提供更全面的信道统计信息，增加各信道详细表格

**新增功能**：

#### 1. 各信道详细统计表格
```python
self.channel_stats = {}  
# {ch: {'attack': {'total':, 'unicast':, 'broadcast':, 'last_time':, 'rate':},
#       'broadcast': {'count':, 'last_time':, 'rate':}}}
```

新增 `channel_tree` (Treeview) 显示：
- 信道号 (1-13)
- 总帧数
- 单播 (数量 + 占比)
- 广播 (数量 + 占比)
- 瞬时速率 (fps)

#### 2. 分离攻击统计与广播统计
- 攻击统计：来自「信道 X 完成」日志
- 广播统计：来自「信道 X 无客户端」日志
- 各自独立计算速率

#### 3. 界面重构
- 右侧面板分为上下两部分：
  - 上部：当前信道信息 + 全局速率 + 各信道详细表格
  - 下部：AP 列表
- 窗口尺寸调整为 1420×960

**解析逻辑增强**：
- 支持不完整的扫描行（`[扫描] 发现 X 个 AP, Y 个`）
- 动态计算 AP 和客户端数量（而非依赖日志中的总数）

**局限性**：
- 仍为纯功能性版本，无品牌信息
- 无波形图

---

### V5.0 - 作者信息与跑马灯版 (serial_visualizerV5.py)

**设计目标**：增加品牌识别度和用户体验

**新增功能**：

#### 1. 作者信息模块
```python
AUTHOR_INFO = {
    "name": "小吴同学电气设计",
    "alternateName": ["t84RT", "小吴同学", ...],
    "url": "https://t84RT.github.io",
    "email": "xiaowu112899@outlook.com",
    "jobTitle": "电气自动化工程师 / 嵌入式开发者 / 网络安全爱好者",
    "description": "...",
    "knowsAbout": [...],
    "sameAs": [多个社交媒体链接]
}
```

#### 2. 跑马灯 (Marquee)
- 在串口控制栏右侧显示滚动文字
- 内容：项目名称 + 运行状态
- 每 120ms 更新一次，营造动态视觉效果

#### 3. 作者信息栏
- 显示作者姓名、邮箱
- 可点击的超链接：GitHub、个人站、B站、CSDN、博客园
- 点击后调用 `webbrowser.open()` 打开浏览器

#### 4. 界面紧凑化
- 窗口尺寸调整为 1080×700
- 日志框高度缩减为 4 行
- 柱状图高度略微降低

**技术细节**：
```python
def start_marquee(self):
    display_len = 40
    # 循环滚动显示
    self.marquee_var.set(display)
    self.marquee_index = (self.marquee_index + 1) % (text_len + 1)
    self.root.after(120, self.start_marquee)
```

**局限性**：
- 无实时波形图
- 无状态栏
- 无导出功能

---

### V6.0 - 仪表盘与波形图版 (serial_visualizerV6.py)

**设计目标**：打造专业级仪表盘体验，提供最全面的数据可视化

**新增功能**：

#### 1. 仪表盘式速率面板
- 使用 Grid 布局，整齐显示 6 项指标：
  - ⏱ 运行时间
  - 📦 总帧数
  - 📊 平均速率
  - 🚀 峰值速率
  - ⚡ 当前速率
  - 🕒 更新时间
- 每项指标带有彩色标识，一目了然

#### 2. 心跳指示灯
- 位于速率面板右下角
- 有数据时显示绿色，高速时变为橙色
- 无数据时显示灰色

```python
self.heartbeat_canvas = tk.Canvas(rate_frame, width=16, height=16)
self.heartbeat_canvas.create_oval(2, 2, 14, 14, fill="gray", tags="heart")
# 更新时：
if self.peak_rate > 0:
    self.heartbeat_canvas.itemconfig("heart", fill="#00cc66")
if self.peak_rate > 50.0:
    self.heartbeat_canvas.itemconfig("heart", fill="#ff9900")
```

#### 3. 实时速率波形图
- 位于速率面板下方
- 显示最近 50 个数据点的速率变化曲线
- 自动适配纵坐标范围
- 带参考线（中位线）

```python
def draw_waveform(self):
    self.rate_history.append(self.current_channel_rate)
    if len(self.rate_history) > 50:
        self.rate_history.pop(0)
    # 绘制折线
    canvas.create_line(points, fill="#0099ff", width=2, smooth=True)
```

#### 4. 底部状态栏
- 显示：连接状态 | 接收速率 | 运行时长
- 实时更新，提供持久的状态反馈

#### 5. 导出日志功能
- 点击「导出日志」按钮
- 弹出文件保存对话框
- 将原始日志保存为 UTF-8 文本文件

#### 6. PanedWindow 布局
- 使用 `ttk.PanedWindow` 实现可调整大小的分栏
- 左侧信息面板与右侧表格面板可拖动调整比例
- 彻底杜绝界面中的灰色空洞

#### 7. 性能优化
- 预编译所有正则表达式
- 减少 UI 刷新频率
- 表格行交替颜色（`oddrow` tag）

#### 8. 表格行交替颜色
```python
self.channel_tree.tag_configure('oddrow', background='#f5f5f5')
# 插入时：
tag = ('oddrow',) if row_count % 2 != 0 else ()
self.channel_tree.insert("", tk.END, values=..., tags=tag)
```

**界面布局（最终版）**：
```
┌──────────────────────────────────────────────────────────────────┐
│ 串口设置栏（端口 | 波特率 | 打开 | 清空 | 导出 | 状态）  跑马灯 │
├──────────────────────────────────────────────────────────────────┤
│  ┌─────────────┬──────────────────────────────────────────────┐ │
│  │  柱状图      │  PanedWindow (可调比例)                    │ │
│  │  (Canvas)    │  ┌──────────┬───────────────────────────┐ │ │
│  │              │  │ 信息面板  │  各信道详细统计表格       │ │ │
│  │              │  │ - 当前信道 │  CH | Total | Unicast   │ │ │
│  │              │  │ - 速率仪表 │  Broadcast | Rate       │ │ │
│  │              │  │ - 心跳灯  │                           │ │ │
│  │              │  │ - 波形图  │                           │ │ │
│  │              │  └──────────┴───────────────────────────┘ │ │
│  └─────────────┴──────────────────────────────────────────────┘ │
│  AP 列表 (CH | SSID | BSSID | Clients)                        │
├──────────────────────────────────────────────────────────────────┤
│ 原始日志 (ScrolledText, height=6)                              │
├──────────────────────────────────────────────────────────────────┤
│ 作者信息栏 (姓名 | 邮箱 | 超链接)                               │
├──────────────────────────────────────────────────────────────────┤
│ 状态栏 (连接状态 | 接收速率 | 运行时长)                         │
└──────────────────────────────────────────────────────────────────┘
```

---

## 技术架构

### 整体架构图

```
┌─────────────────────────────────────────────────────────────────────┐
│                          用户界面层 (Tkinter)                      │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌─────────┐ │
│  │ 串口控制  │ │ 柱状图   │ │ 统计表格 │ │ AP列表   │ │ 日志框  │ │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘ └─────────┘ │
│       ↑            ↑            ↑            ↑           ↑        │
│       └────────────┴────────────┴────────────┴───────────┘        │
│                            UI 更新引擎                             │
│                              ↑                                    │
├──────────────────────────────┼─────────────────────────────────────┤
│                          业务逻辑层                                │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                    日志解析引擎                              │ │
│  │  ┌────────────┐  ┌────────────┐  ┌──────────────────────┐ │ │
│  │  │ 信道统计解析 │  │ 广播统计解析│  │ AP/STA 扫描解析     │ │ │
│  │  └────────────┘  └────────────┘  └──────────────────────┘ │ │
│  └──────────────────────────────────────────────────────────────┘ │
│                              ↑                                    │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                    数据模型层                                │ │
│  │  channel_data | channel_stats | ap_list | channel_broadcast │ │
│  └──────────────────────────────────────────────────────────────┘ │
│                              ↑                                    │
├──────────────────────────────┼─────────────────────────────────────┤
│                          通信层                                   │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │              串口通信模块 (pySerial + 多线程)                │ │
│  │  ┌────────────┐  ┌────────────┐  ┌──────────────────────┐ │ │
│  │  │ 端口扫描   │  │ 异步读取   │  │ 数据解码 (UTF-8)    │ │ │
│  │  └────────────┘  └────────────┘  └──────────────────────┘ │ │
│  └──────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────────┘
```

### 模块划分

| 模块 | 文件 | 职责 |
|------|------|------|
| **主程序** | `serial_visualizerV6.py` | 应用入口，初始化 GUI 和核心组件 |
| **ChannelVisualizer** | 同上 | 主控制器，协调所有子模块 |
| **串口通信** | 内嵌于主类 | 端口扫描、连接管理、异步读取 |
| **日志解析** | `parse_line()` | 正则匹配、数据提取、模型更新 |
| **UI 更新** | `update_ui()` | 刷新所有可视化组件 |
| **绘图引擎** | `draw_chart()`, `draw_waveform()` | Canvas 绘制柱状图和波形图 |
| **数据模型** | 实例变量 | 存储所有解析后的数据 |

### 数据流设计

```
串口设备 → pySerial → 读取线程 → 队列 → 主线程
                                          ↓
                                    逐行解析 (parse_line)
                                          ↓
                                    更新数据模型
                                          ↓
                                    UI 更新 (update_ui)
                                          ↓
                          ┌───────────────┼───────────────┐
                          ↓               ↓               ↓
                      柱状图刷新      表格刷新        波形图刷新
```

**关键设计决策**：
1. **读写分离**：串口读取在独立线程中运行，通过 `queue.Queue` 传递数据，避免阻塞 GUI 主线程
2. **定时轮询**：主线程每隔 50-100ms 检查队列，批量处理数据
3. **增量更新**：每次只更新变化的部分，而非完全重绘

---

## 安装与使用

### 环境要求

- **操作系统**：Windows / macOS / Linux
- **Python 版本**：3.7 或更高
- **依赖库**：
  - `pyserial`：串口通信
  - `tkinter`：GUI 框架（Python 标准库）

### 安装步骤

#### 1. 克隆仓库

```bash
git clone https://github.com/t84RT/serial_visualizer.git
cd serial_visualizer
```

#### 2. 安装依赖

```bash
pip install pyserial
```

> 注：`tkinter` 通常随 Python 一起安装。若提示缺少，请参考：
> - Ubuntu/Debian: `sudo apt-get install python3-tk`
> - macOS: 通常已包含
> - Windows: 已包含

#### 3. 运行程序

```bash
python serial_visualizerV6.py
```

### 快速开始

#### 步骤 1：连接设备

将运行 WiFi 攻击固件的设备（如 ESP8266、ESP32）通过 USB 串口连接到电脑。

#### 步骤 2：配置串口

1. 点击「刷新」按钮，从下拉列表中选择正确的串口号
2. 确认波特率与设备固件设置一致（默认 115200）

#### 步骤 3：打开串口

点击「打开串口」按钮，状态栏显示「已连接」即表示成功。

#### 步骤 4：开始监控

设备开始输出日志后，界面将自动更新：
- 柱状图显示各信道帧分布
- 表格显示详细统计
- 波形图显示速率变化趋势

#### 步骤 5：数据管理

- **清空显示**：重置所有数据和图表
- **导出日志**：将原始日志保存为文本文件

### 日志格式说明

本工具针对以下日志格式进行解析（以 ESP8266/ESP32 Deauth 攻击固件为例）：

#### 1. 信道完成统计（三行一组）

```
信道 1 完成，总 1234 帧
  ├─ 单播: 800 (65%)
  └─ 广播: 434 (35%)
```

解析结果：
- 信道：1
- 总帧数：1234
- 单播：800 (65%)
- 广播：434 (35%)

#### 2. 无客户端广播

```
信道 6 无客户端，广播 56
```

解析结果：
- 信道：6
- 广播包数：56

#### 3. 扫描结果

```
[扫描] 发现 5 个 AP, 12 个客户端
[AP 1] CH:1 SSID:MyWiFi BSSID:AA:BB:CC:DD:EE:FF
  └─ STA 11:22:33:44:55:66
  └─ STA 77:88:99:AA:BB:CC
[AP 2] CH:6 SSID:GuestNet BSSID:FF:EE:DD:CC:BB:AA
  └─ STA AA:BB:CC:DD:EE:FF
```

解析结果：
- AP 列表：包含信道、SSID、BSSID
- 每个 AP 下关联的 STA（客户端）MAC 地址

#### 4. 不完整扫描行（容错处理）

```
[扫描] 发现 3 个 AP, 8 个
```

工具会标记为待完成状态，等待后续行补充。

---

## 界面详解

### 主界面布局

#### 顶部：串口控制栏 + 跑马灯

| 控件 | 说明 |
|------|------|
| 端口下拉框 | 显示所有可用串口设备 |
| 刷新按钮 | 重新扫描串口设备 |
| 波特率下拉框 | 支持 9600~115200 |
| 打开/关闭串口 | 切换连接状态 |
| 清空显示 | 重置所有数据 |
| 导出日志 | 保存原始日志到文件 (V6.0+) |
| 状态标签 | 显示连接状态和波特率 |
| 跑马灯 | 滚动显示项目信息和运行状态 (V5.0+) |

#### 中部左侧：柱状图

- 横轴：信道 1~13
- 纵轴：帧数/广播数（自动缩放）
- 红色柱：当前攻击信道
- 蓝色柱：其他信道
- 柱顶显示具体数值

#### 中部右侧上：信息面板 (PanedWindow 左侧)

**当前信道统计**：
- 当前攻击信道号
- 总帧数
- 单播 (数量 + 占比)
- 广播 (数量 + 占比)
- 总 AP 数 / 总客户端数

**全局速率统计 (仪表盘)**：
| 指标 | 说明 |
|------|------|
| ⏱ 运行时间 | 从打开串口开始的累计时间 |
| 📦 总帧数 | 累计接收到的总帧数 |
| 📊 平均速率 | 总帧数 / 运行时间 |
| 🚀 峰值速率 | 历史最高瞬时速率 |
| ⚡ 当前速率 | 当前信道的瞬时速率 |
| 🕒 更新时间 | 最后一次数据更新的时间 |

**心跳指示灯**：
- 灰色：无数据
- 绿色：正常接收
- 橙色：高速接收 (>50 fps)

**实时速率波形图**：
- 显示最近 50 个数据点的速率变化
- 蓝色折线，平滑曲线
- 灰色虚线参考线

#### 中部右侧下：各信道详细统计表格 (PanedWindow 右侧)

| 列 | 说明 |
|----|------|
| CH | 信道号 (1-13) |
| Total | 该信道累计总帧数 |
| Unicast | 单播数量 (占比) |
| Broadcast | 广播数量 (占比) |
| Rate | 瞬时速率 (fps) |

- 行交替颜色 (白/灰)，便于阅读
- 数据实时更新

#### 中部右侧下：AP 列表

| 列 | 说明 |
|----|------|
| CH | AP 所在信道 |
| SSID | 网络名称 |
| BSSID | AP 的 MAC 地址 |
| Clients | 关联的客户端 MAC 列表 |

#### 底部：原始日志

- 显示完整的串口输出
- 自动滚动到最新行
- 支持复制、选择文本

#### 底部：作者信息栏

- 作者姓名、邮箱
- 可点击的超链接：GitHub、个人站、B站、CSDN、博客园

#### 底部：状态栏 (V6.0+)

- 连接状态：运行中 / 已断开 / 空闲
- 接收速率：实时 pkt/s
- 运行时长：累计秒数

### 交互操作说明

| 操作 | 效果 |
|------|------|
| 点击「刷新」 | 重新扫描串口设备列表 |
| 选择端口 + 点击「打开串口」 | 建立串口连接，开始接收数据 |
| 点击「关闭串口」 | 断开串口连接，停止接收 |
| 点击「清空显示」 | 清空所有数据和图表 |
| 点击「导出日志」 | 保存原始日志到文件 |
| 点击作者超链接 | 在浏览器中打开对应页面 |
| 拖动 PanedWindow 分隔条 | 调整信息面板与表格面板的宽度比例 |

---

## API 参考

### ChannelVisualizer 类

主控制器类，继承自 `object`。

#### 初始化

```python
def __init__(self, root):
    self.root = root
    self.root.title("13 信道实时监控可视化工具 - 小吴同学电气设计")
    self.root.geometry("1290x850")
    # ... 初始化所有变量
    self.setup_ui()
    self.update_ports()
    self.check_queue()
    self.draw_chart()
    self.start_marquee()
    self.update_status_bar()
```

#### 核心属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `serial_port` | `serial.Serial` | 串口对象 |
| `running` | `bool` | 运行状态标志 |
| `read_queue` | `queue.Queue` | 数据队列 |
| `channel_data` | `dict` | 各信道帧统计 |
| `channel_stats` | `dict` | 各信道详细统计 |
| `channel_broadcast` | `dict` | 无客户端信道广播 |
| `ap_list` | `list` | AP 列表 |
| `current_attack_ch` | `int` | 当前攻击信道 |
| `total_frames` | `int` | 累计总帧数 |
| `peak_rate` | `float` | 峰值速率 |
| `current_channel_rate` | `float` | 当前速率 |
| `rate_history` | `list` | 速率历史 (最近50个) |

### 串口操作

#### update_ports()

扫描并更新可用串口列表。

```python
def update_ports(self):
    ports = [p.device for p in serial.tools.list_ports.comports()]
    self.port_combo['values'] = ports
    if ports and not self.port_var.get():
        self.port_var.set(ports[0])
```

#### toggle_serial()

切换串口连接状态。

```python
def toggle_serial(self):
    if self.running:
        self.close_serial()
    else:
        self.open_serial()
```

#### open_serial()

打开串口连接，启动读取线程。

```python
def open_serial(self):
    port = self.port_var.get().strip()
    baud = int(self.baud_var.get())
    self.serial_port = serial.Serial(port, baudrate=baud, ...)
    self.running = True
    self.read_thread = threading.Thread(target=self.read_serial, daemon=True)
    self.read_thread.start()
```

#### close_serial()

关闭串口连接。

```python
def close_serial(self):
    self.running = False
    if self.serial_port and self.serial_port.is_open:
        self.serial_port.close()
    self.serial_port = None
```

#### read_serial()

串口读取线程函数。

```python
def read_serial(self):
    while self.running and self.serial_port and self.serial_port.is_open:
        if self.serial_port.in_waiting:
            data = self.serial_port.read(self.serial_port.in_waiting)
            text = data.decode('utf-8', errors='replace')
            self.read_queue.put(text)
        else:
            time.sleep(0.01)
```

### 日志解析引擎

#### parse_line(line)

解析单行日志，更新数据模型。

**支持的正则表达式** (V6.0 预编译版本)：

| 正则 | 用途 |
|------|------|
| `r'信道 (\d+) 完成，总 (\d+) 帧'` | 信道完成统计 |
| `r'├─ 单播: (\d+) \((\d+)%\)'` | 单播统计 |
| `r'└─ 广播: (\d+) \((\d+)%\)'` | 广播统计 |
| `r'信道 (\d+) 无客户端，广播 (\d+)'` | 无客户端广播 |
| `r'\[扫描\] 发现 (\d+) 个 AP, (\d+) 个客户端'` | 完整扫描结果 |
| `r'\[扫描\] 发现 (\d+) 个 AP, (\d+) 个'` | 不完整扫描结果 |
| `r'\[AP \d+\] CH:(\d+) SSID:(.*?) BSSID:([0-9A-F:]+)'` | AP 信息 |
| `r'└─ STA ([0-9A-F:]+)'` | STA 信息 |

**解析流程**：

```
1. 信道完成行 → 设置 pending_ch, pending_total
2. 单播行 → 设置 pending_uni
3. 广播行 → 设置 pending_broad，若三者齐全则更新 channel_data
4. 无客户端广播行 → 更新 channel_broadcast 和 channel_stats
5. 扫描行 → 清空 ap_list，重置状态
6. AP 行 → 添加到 ap_list
7. STA 行 → 关联到当前 AP
```

#### 数据模型更新示例

```python
# 信道完成统计更新
self.channel_data[ch] = {'total': total, 'unicast': uni, 'broadcast': broad}
self.current_attack_ch = ch

# 速率计算
attack = self.channel_stats[ch]['attack']
now = time.time()
if attack['last_time'] is not None:
    dt = now - attack['last_time']
    if dt > 0:
        attack['rate'] = total / dt
        if attack['rate'] > self.peak_rate:
            self.peak_rate = attack['rate']
attack['last_time'] = now
attack['total'] = total
self.total_frames += total
```

### UI 更新引擎

#### update_ui()

刷新所有 UI 组件。

**更新顺序**：
1. 当前信道统计文本 (`stats_text`)
2. 速率仪表盘 (6 个 StringVar)
3. 心跳指示灯颜色
4. 各信道详细统计表格 (`channel_tree`)
5. AP 列表表格 (`ap_tree`)
6. 柱状图 (`draw_chart()`)
7. 波形图 (`draw_waveform()`)
8. 状态栏 (`status_bar_text`)

#### check_queue()

定时检查队列，处理数据。

```python
def check_queue(self):
    try:
        while True:
            chunk = self.read_queue.get_nowait()
            for line in chunk.splitlines():
                if line.strip():
                    self.log_text.insert(tk.END, line + "\n")
                    self.log_text.see(tk.END)
                    self.parse_line(line)
            self.update_ui()
    except queue.Empty:
        pass
    finally:
        self.root.after(100, self.check_queue)
```

### 绘图引擎

#### draw_chart()

绘制柱状图。

**绘制流程**：
1. 获取 Canvas 尺寸
2. 计算 13 个信道的数据值
3. 确定最大值（用于纵轴缩放）
4. 绘制坐标轴和刻度
5. 绘制每个信道的柱体（红色高亮当前攻击信道）
6. 标注信道号和数值

#### draw_waveform()

绘制实时速率波形图 (V6.0+)。

**绘制流程**：
1. 获取 Canvas 尺寸
2. 将当前速率加入历史列表（最多 50 个点）
3. 计算最大值（用于纵轴缩放）
4. 绘制参考线
5. 绘制折线（平滑曲线）

---

## 性能优化

### 1. 预编译正则表达式 (V6.0+)

将所有正则表达式在 `__init__` 中预编译，避免每次解析时重复编译。

```python
# 优化前
m = re.match(r'信道 (\d+) 完成，总 (\d+) 帧', line)

# 优化后
self.re_channel_complete = re.compile(r'信道 (\d+) 完成，总 (\d+) 帧')
m = self.re_channel_complete.match(line)
```

**性能提升**：约 30-40% 的解析速度提升。

### 2. 队列批量处理

使用 `queue.Queue` 缓存数据，主线程每 100ms 批量处理一次，减少 UI 刷新次数。

### 3. 增量更新

- 表格更新时，先清空再插入，而非逐行修改
- 柱状图完全重绘（Canvas 不支持增量更新）

### 4. 线程分离

串口读取在独立线程中运行，不阻塞 GUI 主线程，保证界面流畅。

### 5. 减少日志框刷新

仅在插入新行时滚动到末尾，而非每次刷新整个文本框。

---

## 常见问题

### Q1: 打开串口时提示「无法打开端口」

**可能原因**：
- 端口被其他程序占用（如 PuTTY、串口调试助手）
- 设备未正确连接
- 权限不足（Linux/macOS 需要 sudo 或加入 dialout 组）

**解决方案**：
1. 关闭其他占用串口的程序
2. 重新插拔 USB 设备
3. Linux: `sudo usermod -a -G dialout $USER` 后重启

### Q2: 柱状图不显示任何数据

**可能原因**：
- 串口未正确打开
- 设备未输出日志
- 日志格式不匹配

**解决方案**：
1. 检查状态栏是否为「已连接」
2. 在原始日志中查看是否有数据输出
3. 确认固件日志格式与本工具匹配

### Q3: 波形图没有曲线

**可能原因**：
- 尚未接收到任何信道完成统计
- 速率历史为空

**解决方案**：
- 等待设备开始输出攻击日志
- 检查是否有「信道 X 完成」的日志行

### Q4: 导出日志时提示「日志为空」

**原因**：原始日志框中没有内容。

**解决方案**：
- 确保已打开串口并接收到数据
- 或先点击「清空显示」再重新开始

### Q5: macOS 上无法识别串口

**解决方案**：
1. 安装 CH340/CP2102 等驱动
2. 检查 `/dev/tty.*` 下的设备文件
3. 使用 `ls /dev/tty.usb*` 或 `ls /dev/cu.usb*` 查看

### Q6: Windows 上提示「无法解码 UTF-8」

**原因**：设备输出的日志包含非 UTF-8 字符。

**解决方案**：
- 工具已使用 `errors='replace'` 参数，会替换无法解码的字符
- 检查固件的字符编码设置

---

## 贡献指南

我们欢迎任何形式的贡献！无论是 Bug 报告、功能建议还是代码提交。

### 如何贡献

1. **Fork 本仓库**
2. **创建您的特性分支**：`git checkout -b feature/amazing-feature`
3. **提交您的更改**：`git commit -m 'Add some amazing feature'`
4. **推送到分支**：`git push origin feature/amazing-feature`
5. **打开 Pull Request**

### 代码规范

- 遵循 PEP 8 编码规范
- 使用 4 个空格缩进
- 添加适当的注释和文档字符串
- 保持与现有代码风格一致

### 报告 Bug

请在 GitHub Issues 中提交 Bug 报告，并包含：
- 操作系统和 Python 版本
- 完整的错误信息
- 重现步骤
- 预期的正确行为

### 功能建议

欢迎提交新功能建议，请说明：
- 功能描述
- 使用场景
- 预期效果

---

## 作者与鸣谢

### 作者

**小吴同学电气设计 (t84RT)**

- **个人站**：[https://t84RT.github.io](https://t84RT.github.io)
- **GitHub**：[https://github.com/t84RT](https://github.com/t84RT)
- **邮箱**：xiaowu112899@outlook.com
- **B站**：[space.bilibili.com/482117704](https://space.bilibili.com/482117704)
- **CSDN**：[blog.csdn.net/weixin_45922157](https://blog.csdn.net/weixin_45922157)
- **博客园**：[cnblogs.com/Student-Wu-s-Electrical-Design-t84RT](https://www.cnblogs.com/Student-Wu-s-Electrical-Design-t84RT/)

**职业背景**：
电气自动化工程师 / 嵌入式开发者 / 网络安全爱好者

**技术领域**：
- 工业自动化控制（PLC 梯形图、ST 语言）
- 嵌入式系统开发（STM32、ESP32、BW16）
- 上位机与 Edge 插件开发（DataHarvest Pro）
- IoT 侧网络安全

### 鸣谢

- **pySerial** 开发团队：提供了稳定可靠的串口通信库
- **Python 社区**：提供了丰富的开发资源和文档
- **所有测试用户**：提供了宝贵的反馈和建议

---

## 许可证

本项目采用 **MIT 许可证**。

```
MIT License

Copyright (c) 2024 小吴同学电气设计 (t84RT)

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

---

## 附录

### 附录 A：版本对比总览

| 特性 | V1.0 | V2.0 | V3.0 | V5.0 | V6.0 |
|------|------|------|------|------|------|
| 串口通信 | ✅ | ✅ | ✅ | ✅ | ✅ |
| 信道统计 | ✅ | ✅ | ✅ | ✅ | ✅ |
| AP/STA 解析 | ✅ | ✅ | ✅ | ✅ | ✅ |
| 柱状图 | ❌ | ✅ | ✅ | ✅ | ✅ |
| 速率统计 | ❌ | ✅ | ✅ | ✅ | ✅ |
| 各信道表格 | ❌ | ❌ | ✅ | ✅ | ✅ |
| 跑马灯 | ❌ | ❌ | ❌ | ✅ | ✅ |
| 作者信息 | ❌ | ❌ | ❌ | ✅ | ✅ |
| 波形图 | ❌ | ❌ | ❌ | ❌ | ✅ |
| 心跳指示灯 | ❌ | ❌ | ❌ | ❌ | ✅ |
| 状态栏 | ❌ | ❌ | ❌ | ❌ | ✅ |
| 导出日志 | ❌ | ❌ | ❌ | ❌ | ✅ |
| 预编译正则 | ❌ | ❌ | ❌ | ❌ | ✅ |
| PanedWindow | ❌ | ❌ | ❌ | ❌ | ✅ |

### 附录 B：日志格式示例

完整的日志输出示例：

```
信道 1 完成，总 1234 帧
  ├─ 单播: 800 (65%)
  └─ 广播: 434 (35%)
信道 2 完成，总 567 帧
  ├─ 单播: 300 (53%)
  └─ 广播: 267 (47%)
信道 3 无客户端，广播 89
信道 4 无客户端，广播 45
[扫描] 发现 3 个 AP, 8 个客户端
[AP 1] CH:1 SSID:HomeWiFi BSSID:AA:BB:CC:DD:EE:FF
  └─ STA 11:22:33:44:55:66
  └─ STA 77:88:99:AA:BB:CC
[AP 2] CH:6 SSID:GuestNet BSSID:FF:EE:DD:CC:BB:AA
  └─ STA AA:BB:CC:DD:EE:FF
[AP 3] CH:11 SSID:OfficeNet BSSID:11:22:33:44:55:66
攻击周期完成，休息 5 秒...
```

### 附录 C：系统要求

| 项目 | 最低要求 | 推荐配置 |
|------|----------|----------|
| CPU | 1 GHz | 2 GHz+ |
| 内存 | 256 MB | 512 MB+ |
| 存储 | 50 MB | 100 MB |
| 显示 | 1024×768 | 1920×1080 |
| Python | 3.7 | 3.11+ |
