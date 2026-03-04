# 智能关节模组 (Smart Joint Module)

> soha_mod_joint | moduleType: actuator | 全能口: 2x

## 概述

**"精密肌肉"** — 用于驱动 Dummy 级机械臂的高精度动力单元。

- TM ID: `urn:soha:tm:soha_mod_joint`
- TD ID: `urn:dev:sn:SOHA-SJ-001`（示例实例）

## 关键特性

- FOC 控制：磁场定向控制，精密力矩输出
- 一体化：集成无刷电机 + 减速器 + 编码器 + 驱动板
- 大扭矩：满足 Dummy 机械臂肩关节负载

## 接口概览

### Actions

| Action | safe | idempotent | 说明 |
|--------|:----:|:----------:|------|
| `setTarget` | ❌ | ❌ | 命令关节移动到目标角度 |
| `emergencyStop` | ✅ | ✅ | 立即停止运动 |

### Properties (status)

| 属性 | 类型 | 说明 |
|------|------|------|
| `angle` | number (degree) | 当前关节角度 |
| `velocity` | number (deg/s) | 当前角速度 |
| `torque` | number (Nm) | 当前扭矩负载 |
| `error` | integer | 错误码，0 = 正常 |
| `ready` | boolean | 模组已初始化 |

### Events

无。关节模组是纯输出设备。

## 待确认

- [ ] 减速器输出角度范围（当前用 ±166°）
- [ ] maxVelocity 和 maxEffort 的实际数值
- [ ] kp/kv PID 增益是否需要可调
