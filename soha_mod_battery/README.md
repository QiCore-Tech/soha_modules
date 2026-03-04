# 电池模组 (Battery Module)

> soha_mod_battery | moduleType: peripheral | 全能口: 2x

## 概述

**"智能心脏"** — 系统的能源中心，搭载 ESP32 主控芯片。

- TM ID: `urn:soha:tm:soha_mod_battery`
- TD ID: `urn:dev:sn:SOHA-BAT-001`（示例实例）

## 关键特性

- 高压输出：直接输出 24V 总线电压
- PD 快充：支持 USB-C PD 输入/输出（可选特性）
- 电量上报：向 AI 上报剩余电量

## 接口概览

### Actions

| Action | safe | idempotent | 说明 |
|--------|:----:|:----------:|------|
| `setOutput` | ❌ | ✅ | 开启/关闭电源输出 |
| `emergencyCutoff` | ✅ | ✅ | 紧急切断电源 |

### Properties (status)

| 属性 | 类型 | 说明 |
|------|------|------|
| `soc` | number (percent) | 电量百分比 |
| `voltage` | number (V) | 电池电压 |
| `current` | number (A) | 电流（正=放电） |
| `temperature` | number (celsius) | 电芯温度 |
| `charging` | boolean | 正在充电 |
| `output_on` | boolean | 电源输出已开启 |
| `error` | integer | 错误码 |
| `ready` | boolean | 模组已初始化 |

### Events

| Event | 说明 |
|-------|------|
| `lowBattery` | SOC 低于临界阈值 |

## 待确认

- [ ] 低电量警告的 SOC 阈值
- [ ] emergencyCutoff 后的恢复方式
- [ ] PD 快充是否 MVP 必须
