# SOHA 词汇表（Vocabulary）

## 概述

`soha-vocab-v1.jsonld` 定义了 SOHA 平台在 W3C WoT TD 基础上的扩展词汇。

通过 JSON-LD `@context` 机制声明 `soha:` 命名空间，标准 WoT 处理器可安全忽略这些扩展字段。

## 命名空间

```
soha: https://soha.ai/vocab/v1#
```

## 词汇定义

| 属性 | 用途 | 适用范围 |
|------|------|----------|
| `soha:moduleType` | 模组分类：`actuator` / `peripheral` / `kit` | 所有模组 |
| `soha:physics` | 物理模型（关节类型、轴向、限位、控制模式） | 所有模组 |
| `soha:interfaceType` | 接口类型：`skill`（组合操作）/ `action`（透传） | 仅 kit |
| `soha:type` | 嵌入式数据类型提示：`f32` / `u8` / `u32` / `bool` | action input/output 字段 |

## 使用方式

在 TD/TM 的 `@context` 中引用：

```json
{
  "@context": [
    "https://www.w3.org/2022/wot/td/v1.1",
    {
      "soha": "https://soha.ai/vocab/v1",
      "saref": "https://saref.etsi.org/core/"
    }
  ]
}
```

## W3C 合规性

SOHA 扩展字段完全通过标准 JSON-LD `@context` 扩展机制实现，符合 W3C WoT TD v1.1 规范。任何不识别 `soha:` 前缀的标准 WoT 处理器会安全跳过这些字段。
