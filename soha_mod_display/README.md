# 低分屏模组 (Display Module)

> soha_mod_display | moduleType: peripheral | 全能口: 2x

## 概述

**"信息显示与表情"** — 低分辨率显示屏。

- TM ID: `urn:soha:tm:soha_mod_display`
- TD ID: `urn:dev:sn:SOHA-DSP-001`（示例实例）

## 关键特性

- 规格：240x240 彩色显示
- 高刷：24fps+ 动态表情或调试信息
- 分布式同步：多屏可同步显示

## 接口概览

### Actions

| Action | safe | idempotent | 说明 |
|--------|:----:|:----------:|------|
| `showExpression` | ❌ | ✅ | 显示预置表情动画 |
| `drawText` | ❌ | ❌ | 在指定位置绘制文字 |
| `fillScreen` | ❌ | ✅ | 单色填充整屏 |
| `setBrightness` | ✅ | ✅ | 设置背光亮度 |

### Properties (status)

| 属性 | 类型 | 说明 |
|------|------|------|
| `width` | integer | 屏幕宽度 (240) |
| `height` | integer | 屏幕高度 (240) |
| `brightness` | number (percent) | 当前亮度 |
| `error` | integer | 错误码 |
| `ready` | boolean | 模组已初始化 |

### Events

无。显示模组是纯输出设备。

## 待确认

- [ ] 预置表情列表（需产品定义）
- [ ] 中文字体支持
- [ ] 多屏同步机制
