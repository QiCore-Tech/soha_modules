# 操控模组 (Controller Module)

> soha_mod_controller | moduleType: peripheral | 全能口: 2x + 顶部专用触点

## 概述

**"百变控制器"** — 物理交互的乐高底座。

- TM ID: `urn:soha:tm:soha_mod_controller`
- TD ID: `urn:dev:sn:SOHA-CTL-001`（示例实例）
- moduleName: `soha_controller_v1`

## 关键特性

- 磁吸/卡槽触点：底座上有 Pogo Pin 触点接口
- 配件识别：按键盖、摇杆盖、旋钮盖自动识别
- 单 TD 全能力：包含所有配件的 properties 和 events

## 接口概览

### Actions

| Action | safe | idempotent | 说明 |
|--------|:----:|:----------:|------|
| `setLongPressThreshold` | ✅ | ✅ | 配置长按检测时间 |

### Properties (status)

| 属性 | 类型 | 配件 | 说明 |
|------|------|------|------|
| `accessory_type` | string | 通用 | 当前配件类型 |
| `button_pressed` | boolean | button | 按钮状态 |
| `button_count` | integer | button | 累计按压次数 |
| `joystick_x` | number | joystick | X 轴 (-1~1) |
| `joystick_y` | number | joystick | Y 轴 (-1~1) |
| `joystick_button` | boolean | joystick | 摇杆按压 |
| `knob_angle` | number (degree) | knob | 累计旋转角度 |
| `knob_pressed` | boolean | knob | 旋钮按压 |
| `error` | integer | 通用 | 错误码 |
| `ready` | boolean | 通用 | 模组已初始化 |

### Events

| Event | 配件 | 说明 |
|-------|------|------|
| `accessoryChanged` | 通用 | 配件插拔 |
| `buttonPressed` | button | 按下 |
| `buttonReleased` | button | 释放 |
| `longPress` | button | 长按 |
| `knobTurned` | knob | 旋转 |
| `joystickMoved` | joystick | 摇杆位置变化（超过死区阈值） |

## 待确认

- [ ] 配件识别机制
- [ ] 是否有更多配件类型
- [ ] 摇杆死区配置
