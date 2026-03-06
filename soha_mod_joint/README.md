# 智能关节模组 (Smart Joint Module)

> soha_mod_joint | moduleType: actuator | 全能口: 2x

## 概述

**"精密肌肉"** — 用于驱动 Dummy 级机械臂的高精度动力单元。

- TM ID: `urn:soha:tm:soha_mod_joint`
- TD ID: `urn:dev:sn:SOHA-SJ-001`（示例实例）
- moduleName: `soha_joint_v1`

## 关键特性

- FOC 控制：磁场定向控制，精密力矩输出
- 一体化：集成无刷电机 + 编码器 + 驱动板
- 无限旋转：无角度限位，支持任意方向连续旋转
- maxVelocity 2.175 rad/s，maxEffort 87 Nm

## 物理参数 (soha:physics)

| 参数 | 值 | 说明 |
|------|-----|------|
| joint.type | continuous | 无限旋转 |
| limits.maxVelocity | 2.175 rad/s | 最大角速度 |
| limits.maxEffort | 87 Nm | 最大扭矩 |
| actuator.type | position | 位置控制 |
| actuator.kp | 1000 | P 增益 |
| actuator.kv | 20 | D 增益 |

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

| Event | 说明 |
|-------|------|
| `targetReached` | 关节角度到达目标（死区内），含剩余误差 |
| `stall` | 扭矩达到上限但位置误差仍存在，电机无法到达目标 |

## 待确认

- [ ] kp/kv PID 增益是否需要可调
