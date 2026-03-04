# 灯光驱动模组 (LED Driver Module)

> soha_mod_led | moduleType: peripheral | 全能口: 2x + LED 灯带接口

## 概述

**"氛围大师"** — 不发光，但控制光。

- TM ID: `urn:soha:tm:soha_mod_led`
- TD ID: `urn:dev:sn:SOHA-LED-001`（示例实例）

## 关键特性

- 通用接口：WS2812/RGB 接口（3pin/4pin）
- 大功率支持：可驱动 1-2 米长灯带
- 内置灯效：rainbow, breathing, chase, blink, gradient

## 接口概览

### Actions

| Action | safe | idempotent | 说明 |
|--------|:----:|:----------:|------|
| `setColor` | ❌ | ✅ | 全部 LED 设为同一颜色 |
| `setPixel` | ❌ | ❌ | 设置单个 LED |
| `setEffect` | ❌ | ✅ | 播放预置灯效 |
| `setBrightness` | ✅ | ✅ | 设置全局亮度 |
| `clear` | ✅ | ✅ | 关闭所有 LED |

### Properties (status)

| 属性 | 类型 | 说明 |
|------|------|------|
| `led_count` | integer | 灯带 LED 数量 |
| `brightness` | number (percent) | 全局亮度 |
| `current_effect` | string | 当前灯效 |
| `error` | integer | 错误码 |
| `ready` | boolean | 模组已初始化 |

### Events

无。

## 待确认

- [ ] 最大支持 LED 数量
- [ ] LED 检测机制
- [ ] 是否支持 RGBW 灯带
