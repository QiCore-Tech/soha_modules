# 智能微舵机 (Smart Micro Servo)

> soha_mod_servo | moduleType: actuator | 全能口: 2x

## 概述

**"入门肌肉"** — 用于 3D 打印玩具、开关触发等轻负载场景。

- TM ID: `urn:soha:tm:soha_mod_servo`
- TD ID: `urn:dev:sn:SOHA-SS-001`（示例实例）
- moduleName: `soha_servo_v1`

## 关键特性

- 双模式：servo（位置控制，无限旋转）↔ wheel（连续旋转速度控制）
- 角度检测：支持角度回读
- 零位校准：软件一键归零
- maxVelocity 6.28 rad/s，maxEffort 0.25 Nm

## 物理参数 (soha:physics)

| 参数 | 值 | 说明 |
|------|-----|------|
| joint.type | continuous | 无限旋转 |
| limits.maxVelocity | 6.28 rad/s | 最大角速度 |
| limits.maxEffort | 0.25 Nm | 最大扭矩 |
| actuator.type | position | 位置控制 |

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

| Event | 说明 |
|-------|------|
| `targetReached` | servo 模式下到达目标角度（死区内） |
| `stall` | 电机堵转检测，可用于夹爪接触判定 |

## 待确认

- [ ] wheel 模式下 angle 回读的含义
