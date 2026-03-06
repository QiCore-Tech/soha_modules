# 高速动力模组 (High-Speed Drive Module)

> soha_mod_drive | moduleType: actuator | 全能口: 2x

## 概述

**"智能车轮"** — 用于遥控车、移动底盘。

- TM ID: `urn:soha:tm:soha_mod_drive`
- TD ID: `urn:dev:sn:SOHA-HSM-001`（示例实例）
- moduleName: `soha_drive_v1`

## 关键特性

- 高转速：maxVelocity 20.94 rad/s，适合轮式移动
- 大扭矩：maxEffort 2.0 Nm
- 电子刹车：支持急停和主动制动
- 开环速度控制：占空比方式调速，back-EMF 估算转速
- 无编码器：速度为估算值，实际速度随负载变化

## 物理参数 (soha:physics)

| 参数 | 值 | 说明 |
|------|-----|------|
| joint.type | continuous | 无限旋转 |
| joint.axis | [0, 0, 1] | Z 轴 |
| limits.maxVelocity | 20.94 rad/s | 最大转速 (~200 RPM) |
| limits.maxEffort | 2.0 Nm | 最大扭矩 |
| actuator.type | velocity | 速度控制 |

## 接口概览

### Actions

| Action | safe | idempotent | 说明 |
|--------|:----:|:----------:|------|
| `setSpeed` | ❌ | ❌ | 设置旋转速度（占空比） |
| `brake` | ✅ | ✅ | 主动制动 |
| `emergencyStop` | ✅ | ✅ | 立即停止 |

### Properties (status)

| 属性 | 类型 | 说明 |
|------|------|------|
| `duty_cycle` | number (percent) | 当前占空比 |
| `velocity` | number (rad/s) | back-EMF 估算转速，随负载变化 |
| `error` | integer | 错误码 |
| `ready` | boolean | 模组已初始化 |

### Events

| Event | 说明 |
|-------|------|
| `stall` | back-EMF 检测到电机堵转或重载 |
| `overcurrent` | 电机电流超过安全阈值 |

## 待确认

- [ ] brake() 和 setSpeed(0) 是否完全等价
- [ ] emergencyStop vs brake 的物理差异
