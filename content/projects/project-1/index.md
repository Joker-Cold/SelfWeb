---
title: "数字实境- 基于紫光FPGA的远程教学实验平台"
summary: "2025 全国大学生集成电路创新创业大赛（集创赛）总决赛 国家三等奖 作品 —— FPGA + RK3588 + STM32 异构平台，集成 PCIe DMA、USBIP、HMCAD1511 高速 ADC 与 LVGL 上位机。"
date: 2025-07-11
tags: ["FPGA", "RK3588", "STM32", "PCIe", "Linux", "LVGL", "USBIP", "C/C++", "Verilog"]
showToc: true
---

## 项目概览

本项目是 **2025 年全国大学生集成电路创新创业大赛（集创赛）** 总决赛的参赛作品（参赛编号 **CICC0901808**），最终荣获 **全国总决赛国家三等奖**。作品以紫光同创 FPGA 为核心，配合 **RK3588** Linux 主控与 **STM32** 协处理器，构建了一个面向高校教学与科研的多功能实验平台，覆盖从基础 IO 到高速数据采集与远程调试的完整链路。

> 🏆 **获奖**：2025 全国大学生集成电路创新创业大赛 全国总决赛 **国家三等奖**

## 项目海报

{{< figure src="poster.png" alt="集创赛作品海报" >}}

## 演示视频

{{< bilibili BV12BDhBsEw2 >}}

如未正常显示，请直接访问：[bilibili.com/video/BV12BDhBsEw2](https://www.bilibili.com/video/BV12BDhBsEw2/)

## 系统架构

平台由三类计算单元协同工作：

- **FPGA 主控**：承担逻辑生成、波形采样、PCIe DMA 与高速接口控制
- **RK3588 (ARM64 Linux)**：负责上层应用、网络服务、USBIP 转发与远程升级
- **STM32**：作为 ADDA 板卡上的 I²C 从设备，实现传感器与外设接入

主机端通过 **LVGL** 移植的 Windows 上位机与平台交互，可远程显示采集波形、配置参数并执行 OTA 升级。

## 核心功能模块

### 1. PCIe DMA 高速通路
- 基于紫光官方 Linux PCIe 驱动二次开发
- FPGA ↔ RK3588 之间实现高带宽数据通路，用于 ADC 采样数据回传

### 2. HMCAD1511 高速 ADC 采集
- FPGA 端实现 HMCAD1511 接口与采样链路
- 配合上位机完成实时波形显示

### 3. USBIP 远程外设接入
- 定制 RK3588 Linux 内核（5.10.160）开启 USBIP
- 将板载 USB 设备远程映射到 Windows 主机，便于教学场景调试

### 4. LVGL 上位机
- 将 LVGL 移植到 Windows，构建跨平台波形显示与控制 UI
- 通过 UDP 与 Linux 端服务器通信

### 5. UART 远程升级（OTA）
- Linux 端实现基于串口的远程固件升级工具
- 支持 FPGA / MCU 现场迭代

### 6. 教学项目库
配套提供从入门到进阶的 9 个 FPGA 教学例程：

| 基础项目                   | 高阶项目        |
| -------------------------- | --------------- |
| 呼吸灯 / 流水灯 / 按键控制 | I²C 传感器      |
| 倒计时                     | 密码锁          |
| HDMI Colorbar              | 交通灯 / WS2812 |


## 资料获取

技术文档、技术分享论文、答辩 PPT 以及完整源码包（FPGA 工程、PCIe 驱动、STM32 工程等）体积较大，未随站点部署，可邮箱或github联系获取。
