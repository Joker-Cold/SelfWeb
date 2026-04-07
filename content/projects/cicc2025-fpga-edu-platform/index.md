---
title: "数字实境 — 基于紫光 FPGA 的远程教学实验平台"
summary: "面向高校 FPGA 教学场景的异构远程实验平台 —— 紫光 PG2L100H FPGA + RK3588 Linux 主控 + STM32 + 自研 ADDA 板卡，通过 PCIe DMA、USBIP 远程 JTAG、LVGL 上位机与远程摄像头打通采集、调试、显示与升级全链路。2025 全国大学生集成电路创新创业大赛（集创赛）总决赛国家三等奖作品。"
date: 2025-07-11
tags: ["FPGA", "紫光同创", "RK3588", "STM32", "PCIe DMA", "USBIP", "Linux", "LVGL", "数字孪生", "教学平台"]
showToc: true
---

## 项目概览

本项目是 **2025 年全国大学生集成电路创新创业大赛（集创赛）** 总决赛参赛作品，最终荣获 **全国总决赛国家三等奖**。作品面向高校 FPGA 教学与远程实验场景，构建了一套从模拟前端到上位机 UI 完整闭环的实验平台。

整体方案以 **紫光同创 PG2L100H FPGA** 作为核心控制板，**RK3588 (ARM64 Linux)** 作为上位机服务器，**STM32** 作为自研 ADDA 板卡的本地控制器，再加上一块自研低成本 ADDA 板卡和一颗远程摄像头，组成一台「教师可远程下发实验、学生可远程烧录调试、波形/视频实时回传」的数字孪生型实验平台。

## 项目海报

{{< figure src="poster.png" alt="集创赛作品海报" >}}

## 演示视频

{{< bilibili BV12BDhBsEw2 >}}

如未正常显示，请直接访问：[bilibili.com/video/BV12BDhBsEw2](https://www.bilibili.com/video/BV12BDhBsEw2/)

## 系统架构

{{< figure src="architecture.png" alt="系统架构方框图" caption="系统总体架构：ADDA 模拟前端 ↔ PG2L100H FPGA ↔ RK3588 Linux ↔ PC，外加 JTAG-Debugger 与远程摄像头" >}}

整个数据通路是一条贯穿模拟前端到用户终端的"一线通"，每一段链路的物理介质与带宽都经过测算：

{{< figure src="dataflow.png" alt="一线通数据流向" caption="一线通数据流：UART (115200 bps) → PCIe (~100 MB/s) → 以太网 (~100 MB/s)" >}}

| 计算单元                 | 角色                                                  |
| ------------------------ | ----------------------------------------------------- |
| **紫光 PG2L100H FPGA**   | 核心控制板，承担逻辑生成、ADC/DAC 时序、PCIe DMA      |
| **RK3588 (ARM64 Linux)** | 上位机服务器，负责网络服务、USBIP、远程升级、视频推流 |
| **STM32**                | ADDA 板卡本地控制器，通过 UART 与 FPGA 协同           |
| **PC (Windows)**         | LVGL 上位机 UI，远程显示波形与下发命令                |

## 硬件设计

### 1. 自研低成本 ADDA 板卡

为降低教学场景的部署成本，自行设计并焊接了一块 ADDA 模拟前端板卡：

- **ADC**：AD9288，双通道、约 100 MSPS 采样率，由 FPGA 直接采集
- **DAC**：DAC908，约 165 MSPS 输出
- **模拟前端**：
  - **AQY282** 实现交直流耦合切换
  - **AD8065** 用作缓冲放大
  - **AD603** 提供 -11 ~ +31 dB 程控增益（实现 5 mV/div ~ 200 mV/div 量程）
  - **ADA4932** 单端转差分，驱动 AD9288 的差分输入端
- **本地控制器**：STM32 通过 UART 与 FPGA 通信，配置增益、采样模式等
- **成本**：BOM ≈ 200 元，相比商用方案大幅降低

{{< figure src="pcb-adda.jpg" alt="自研 ADDA 板 PCB Layout" caption="自研 ADDA 板 PCB Layout —— 双通道输入，集成 AD9288 / DAC908 / AD8065 / AD603 / ADA4932 与 STM32 控制" >}}

### 2. 拓展板卡 (FMC)

采用标准 **FMC 接口** 设计的拓展板插槽，支持各类教学扩展板**即插即用**，方便教师按教学内容灵活配置实验场景。

## 软件设计

### 1. PCIe DMA 高速通路

- 基于紫光同创官方 Linux PCIe 例程二次开发
- 在 FPGA ↔ RK3588 间建立高带宽 DMA 通路
- 用于将 ADC 采样数据高速回传到 Linux 侧

### 2. USBIP 远程 JTAG 调试

将板载 JTAG 调试器通过 USBIP 协议远程映射到 Windows 主机，使学生能够在远端：

- 完成 FPGA **Configuration 下载**
- 完成 **bin 文件烧录**
- 完成 **adc/eg 等烧录**
- 与板载逻辑分析仪做实时数据交互

实现上需要**重新编译 RK3588 Linux 内核**以启用 USBIP 模块。

{{< figure src="usbip.png" alt="USBIP 客户端界面" caption="Windows 端 USBIP 客户端：FT2232 JTAG 适配器已通过 RK3588 远程映射 (192.168.1.101:3240)" >}}

{{< figure src="jtag-debugger.png" alt="紫光逻辑分析仪通过 USBIP 抓波形" caption="紫光官方 JTAG Debugger 通过 USBIP 远程连接 FPGA，实时采集 ADC 通道波形" >}}

### 3. UART 远程升级

Linux 端实现了基于串口的远程升级工具 `remote_hwupgrade`，可在不接触现场板卡的情况下完成 FPGA / MCU 的固件迭代。

### 4. 远程摄像头与 RTSP 推流

接入 2K30 帧远程摄像头，由 RK3588 通过 RTSP 推流到上位机，实现物理实验现场的可视化监控，构成"数字孪生"的视觉闭环。

### 5. LVGL Windows 上位机 (`lv_port_vscode`)

将 **LVGL** 框架移植到 Windows，构建跨平台波形显示与控制 UI：

- 全部使用纯 C 实现，编译/调试基于 VSCode 工程
- 通过 **UDP** 与 Linux 端服务器通信
- 集成实时波形显示、参数配置、设备管理等功能

{{< figure src="lvgl-ui.jpg" alt="LVGL Windows 上位机 UI" caption="LVGL 上位机：双通道示波器界面，包含耦合方式、垂直/水平档位、触发源/电平/模式、DDS 信号源等控件" >}}

### 6. 软件架构总览

```
PANGO FPGA  ──1.PCIe DMA──▶  RK3588 Linux Server  ──ETH/UDP──▶  Windows UI
            ──2.UART─────▶  ├ udp_test (Server App)  ──USBIP──▶  (lv_port_vscode)
                            ├ remote_hwupgrade (UART Burn)
                            └ USBIP (JTAG Forwarding)
```

## 配套教学项目库

围绕平台同时配套了 9 个由浅入深的 FPGA 教学例程，覆盖典型教学场景：

| 入门项目                   | 进阶项目        |
| -------------------------- | --------------- |
| 呼吸灯 / 流水灯 / 按键控制 | I²C 传感器      |
| 倒计时                     | 密码锁          |
| HDMI Colorbar              | 交通灯 / WS2812 |

每个例程都配套实验板卡、参考工程与说明文档，作为教学闭环的最后一环。

## 资料获取

技术文档、技术分享论文、答辩 PPT 以及完整源码包（FPGA 工程、PCIe 驱动、STM32 工程、Linux 内核 patch、LVGL 上位机源码等）体积较大，未随站点部署。
