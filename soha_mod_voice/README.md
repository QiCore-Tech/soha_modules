# 语音交互模组 (Voice Interaction Module)

> soha_mod_voice | moduleType: peripheral | 全能口: 2x

## 概述

**"嘴巴与耳朵"** — 双向音频交互。

- TM ID: `urn:soha:tm:soha_mod_voice`
- TD ID: `urn:dev:sn:SOHA-VOI-001`（示例实例）

## 关键特性

- 双向音频：集成麦克风阵列（听）+ 扬声器驱动（说）
- 本地关键词识别：支持自定义关键词指令识别

## 接口概览

### Actions

| Action | safe | idempotent | 说明 |
|--------|:----:|:----------:|------|
| `playTone` | ❌ | ❌ | 播放指定频率音调 |
| `setVolume` | ✅ | ✅ | 设置音量 |
| `startListening` | ✅ | ✅ | 开始关键词监听 |
| `stopListening` | ✅ | ✅ | 停止监听 |
| `setKeywords` | ❌ | ✅ | 配置关键词列表 |

### Properties (status)

| 属性 | 类型 | 说明 |
|------|------|------|
| `is_listening` | boolean | 关键词检测是否活跃 |
| `volume` | number (percent) | 当前音量 |
| `keyword_count` | integer | 已配置关键词数 |
| `error` | integer | 错误码 |
| `ready` | boolean | 模组已初始化 |

### Events

| Event | 说明 |
|-------|------|
| `keywordDetected` | 识别到配置的关键词 |

## 待确认

- [ ] 关键词识别的语言支持
- [ ] 最大关键词数量和长度限制
- [ ] 是否支持 WAV/PCM 音频播放
