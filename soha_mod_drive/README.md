# 高速动力模组 (High-Speed Drive Module)

> soha_mod_drive | moduleType: actuator | 全能口: 2x

## 概述

**"智能车轮"** — 用于遥控车、移动底盘。

- TM ID: `urn:soha:tm:soha_mod_drive`
- TD ID: `urn:dev:sn:SOHA-HSM-001`（示例实例）

## 关键特性

- 高转速：适合轮式移动
- 电子刹车：支持急停和主动制动
- 开环速度控制：占空比方式调速

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
| `error` | integer | 错误码 |
| `ready` | boolean | 模组已初始化 |

### Events

无。

## 待确认

- [ ] 最大转速
- [ ] brake() 和 setSpeed(0) 是否完全等价
- [ ] emergencyStop vs brake 的物理差异
