# 交换机模组 (Switch Hub)

> soha_mod_switch | moduleType: peripheral | 全能口: 4x | 公版模块

## 概述

**"神经中枢"** — 用于复杂拓扑的分流与扩展。

- TM ID: `urn:soha:tm:soha_mod_switch`
- TD ID: `urn:dev:sn:SOHA-SW-001`（示例实例）
- moduleName: `soha_switch_v1`

## 关键特性

- 星型扩展：4 条支路（如四足机器人的躯干中心）
- 信号整形：增强信号，支持更长距离传输

## 接口概览

### Actions

| Action | safe | idempotent | 说明 |
|--------|:----:|:----------:|------|
| `reset` | ✅ | ✅ | 重启交换机 |

### Properties (status)

| 属性 | 类型 | 说明 |
|------|------|------|
| `port_count` | integer | 端口总数 (4) |
| `connected_ports` | array[boolean] | 各端口连接状态 |
| `error` | integer | 错误码 |
| `ready` | boolean | 模组已初始化 |

### Events

| Event | 说明 |
|-------|------|
| `portChanged` | 端口连接/断开 |

## 待确认

- [ ] 端口数是否固定为 4
- [ ] 是否需要上报信号质量指标
