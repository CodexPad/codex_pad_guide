# CodexPad连接使用指南：使用BLE转串口模块

## 概述

本文档适用于**通过 BLE 转串口模块接收 CodexPad 手柄数据**的场景。如果您的硬件平台（如 Arduino UNO、Nano等）不具备 BLE 通信能力，需要通过外接 BLE 转串口模块（如 NL-16）或使用板载集成 BLE 转串口模块的开发板（如 BLE-UNO）来接收手柄数据，请遵循本指南。

无论 BLE 转串口模块是外接的还是开发板板载集成的，其工作原理相同：蓝牙芯片负责无线收发，主控通过串口与蓝牙芯片通信。您将通过串口发送 AT 指令来控制蓝牙模块的连接，以及接收蓝牙芯片传来的数据帧。

## 连接方式说明

本方案通过外接或板载的 BLE 转串口模块建立连接，仅支持 **Bluetooth Device Address 直连**方式。

此方式通过手柄唯一的 [Bluetooth Device Address](bluetooth_device_address.zh-CN.md#bluetooth-device-address) 进行精准连接。

- **工作原理**：在您的主机代码中，预先写入目标手柄的 Bluetooth Device Address，通过 AT 指令配置 BLE 转串口模块为主机模式后发起连接。程序启动后将直接尝试与这个特定地址的设备建立连接。

- **核心特点**：**指向明确，连接稳定**。适用于开发环境固定、手柄与主机配对关系确定的场景（例如，某台机器人固定使用某个特定手柄控制）。

- **使用前准备**：请根据您的 CodexPad 型号，查阅对应产品文档以获取 Bluetooth Device Address (BD_ADDR) 的具体操作方法，并完成记录。

## 连接与使用

### Arduino IDE库和示例代码

本库及示例代码适用于**通过 BLE 转串口模块接收 CodexPad 手柄数据**的场景，支持以下两种连接方案：

- 开发板板载集成 BLE 转串口模块

    适用于**板载了 BLE 转串口模块**的开发板（如 BLE-UNO）。主控通过串口与蓝牙芯片进行通信，工作原理与 “开发板 + 外接 BLE 转串口模块” 相同。

    | 适用开发板 |
    | :-------- |
    | BLE-UNO   |

- 开发板外接 BLE 转串口模块

    适用于**没有板载集成 BLE 转串口模块**的开发板（如 Arduino UNO、Nano 等），通过外接 BLE 转串口模块（如 NL-16）来使用 CodexPad 手柄。

    **支持的外接 BLE 转串口模块**

    | 支持的模块 |
    | :------------ |
    | NL-16 (V1.2+) |

    **支持的硬件平台**

    | 支持的硬件平台 |
    | :----------- |
    | Arduino UNO  |
    | Arduino Nano |

**详细说明**：[CodexPad Frame Decoder Arduino lib](../../../codex_pad_frame_decoder_arduino_lib/blob/main/README.zh-CN.md#codexpad-frame-decoder-arduino-lib)
