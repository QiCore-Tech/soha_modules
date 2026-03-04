# 智能微舵机 (Smart Micro Servo)

> soha_mod_servo | moduleType: actuator | 全能口: 2x

## 概述

**"入门肌肉"** — 用于 3D 打印玩具、开关触发等轻负载场景。

- TM ID: `urn:soha:tm:soha_mod_servo`
- TD ID: `urn:dev:sn:SOHA-SS-001`（示例实例）

## 关键特性

- 双模式：servo（位置控制 ±180°）↔ wheel（360° 连续旋转）
- 角度检测：支持角度回读
- 零位校准：软件一键归零

## 接口概览

### Actions

| Action | safe | idempotent | 说明 |
|--------|:----:|:----------:|------|
| `setTarget` | ❌ | ❌ | servo 模式：移动到目标角度 |
| `setSpeed` | ❌ | ❌ | wheel 模式：设置旋转速度 |
| `setMode` | ❌ | ✅ | 切换 servo/wheel 模式 |
| `calibrateZero` | ✅ | ✅ | 设置当前位置为零位 |
| `emergencyStop` | ✅ | ✅ | 立即停止 |

### Properties (status)

| 属性 | 类型 | 说明 |
|------|------|------|
| `angle` | number (degree) | 当前角度 |
| `velocity` | number (deg/s) | 当前角速度 |
| `mode` | string | 当前模式：servo / wheel |
| `error` | integer | 错误码 |
| `ready` | boolean | 模组已初始化 |

### Events

无。

## 待确认

- [ ] servo 模式实际角度范围
- [ ] wheel 模式下 angle 回读的含义
- [ ] maxVelocity 和 maxEffort 的实际数值
