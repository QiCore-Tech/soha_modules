# 开发者模组 (Developer Bridge Module)

> soha_mod_developer | moduleType: peripheral | 全能口: 2x + 2x Qwiic + 1x 排母区

## 概述

**"特洛伊木马"** — 兼容旧世界的万能转接器。

- TM ID: `urn:soha:tm:soha_mod_developer`
- TD ID: `urn:dev:sn:SOHA-DEV-001`（示例实例）
- moduleName: `soha_developer_v1`

## 关键特性

- 接口兼容：2x STEMMA QT/Qwiic + 1 组 GVS 排母
- 协议翻译：AI 根据数据手册自动生成驱动代码
- 原始 I/O：提供 I2C 读写 + GPIO 读写

## 接口概览

### Actions

| Action | safe | idempotent | 说明 |
|--------|:----:|:----------:|------|
| `i2cScan` | ✅ | ✅ | 扫描 I2C 设备 |
| `i2cWrite` | ❌ | ❌ | I2C 写入 |
| `i2cRead` | ✅ | ✅ | I2C 读取 |
| `gpioSetMode` | ❌ | ✅ | 配置 GPIO 方向 |
| `gpioWrite` | ❌ | ❌ | GPIO 输出 |
| `gpioRead` | ✅ | ✅ | GPIO 读取 |

### Properties (status)

| 属性 | 类型 | 说明 |
|------|------|------|
| `gpio_count` | integer | 可用 GPIO 数量 |
| `i2c_count` | integer | 可用 I2C/Qwiic 端口数 |
| `error` | integer | 错误码 |
| `ready` | boolean | 模组已初始化 |

### Events

| Event | 说明 |
|-------|------|
| `gpioInterrupt` | GPIO 输入引脚电平变化（上升沿/下降沿） |

## 待确认

- [ ] GPIO 引脚数量（当前假设 8 个）
- [ ] 是否支持 SPI 和 UART
- [ ] 是否需要模拟输入（ADC）
