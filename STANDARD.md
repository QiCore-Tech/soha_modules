# SOHA 模组开发标准

本文档定义了开发新 SOHA 模组的规范和标准。所有新模组必须遵循此标准。

---

## 1. 架构概述

SOHA 采用**全局基座 TM + 模块 TD** 的两层架构：

- **`tm/soha_base.tm.json`**：唯一的 Thing Model，定义所有模组共有的 `status.error` 和 `status.ready` 属性。托管在公网服务器 `https://soha.ai/tm/`。
- **`soha_mod_xxxx.td.json`**：每个模块一个 Thing Description，包含完整的模块特有定义（properties、actions、events）+ 实例参数（id、base、security）。由嵌入式设备存储并直接返回。

### 1.1 交互流程

```
上位机 ──GET TD──▶ 嵌入式设备（直接 dump，零解析开销）
       ◀─完整TD──

上位机 ──GET TM──▶ soha.ai/tm/soha_base.tm.json（有缓存，只取一次）
       ◀─基座TM──

上位机合并：TD 模块定义 + base TM 公共属性 = 完整交互模型
```

### 1.2 设计原则

- **TD 是唯一事实源**：每个模块的全部特有定义都在 TD 中，不存在单独的 per-module TM
- **设备端零解析**：嵌入式设备只需将 flash 中的 TD 原样输出，不需要运行时 JSON 操作
- **版本一致性由固件保证**：TD 随固件烧录，避免设备与服务器定义不同步

---

## 2. 命名规范

- 目录名：`soha_mod_xxxx`，全小写，下划线分隔
- TD 文件：`soha_mod_xxxx.td.json`
- TD ID：`urn:dev:sn:SOHA-XXX-NNN`（XXX = 模组缩写，NNN = 序列号）

---

## 3. 目录结构

```
soha_modules/
├── tm/
│   └── soha_base.tm.json             # 全局基座 TM（唯一）
├── _template/
│   └── module.td.json.template       # TD 模板
├── soha_mod_xxxx/
│   ├── README.md                     # 模组介绍（中文）
│   ├── soha_mod_xxxx.td.json         # Thing Description（完整定义）
│   └── mesh/                         # 3D 模型（预留）
│       └── .gitkeep
```

---

## 4. 全局基座 TM

`tm/soha_base.tm.json` 定义所有模组共有的属性，TD 通过 `links` 引用：

```json
{
  "@type": "tm:ThingModel",
  "properties": {
    "status": {
      "type": "object", "readOnly": true, "observable": true,
      "properties": {
        "error": { "type": "integer", "description": "Error code. 0 = normal" },
        "ready": { "type": "boolean", "description": "Module initialized and ready" }
      },
      "forms": [{ "href": "/properties/status", "op": ["readproperty"] }]
    }
  }
}
```

TD 中的引用方式：

```json
"links": [{
  "rel": "type",
  "href": "https://soha.ai/tm/soha_base.tm.json",
  "type": "application/tm+json"
}]
```

**注意**：`error` 和 `ready` 由基座 TM 提供，TD 的 `status.properties` 中**不应**重复定义这两个字段。

---

## 5. TD 必填字段

### 5.1 顶层必填

| 字段 | 类型 | 说明 |
|------|------|------|
| `@context` | array | 必须含 `"https://www.w3.org/2022/wot/td/v1.1"` + `soha`/`saref` 命名空间 |
| `@type` | array | 具体设备类型（如 `["saref:Actuator", "soha:ContinuousJoint"]`） |
| `id` | string | 设备实例标识 `"urn:dev:sn:SOHA-XXX-NNN"` |
| `title` | string | 英文人类可读名称 |
| `description` | string | 英文功能描述 |
| `base` | string | CoAP 端点 `"coap://<ip>:<port>"` |
| `securityDefinitions` | object | 安全方案定义 |
| `security` | string | 安全方案引用 |
| `links` | array | 必须引用 `soha_base.tm.json` |
| `soha:moduleType` | string | `"actuator"` / `"peripheral"` / `"kit"` |
| `soha:moduleName` | string | 模组全局唯一标识符（如 `"soha_joint_v1"`） |
| `soha:physics` | object | 物理模型（见 §7） |
| `properties` | object | 至少包含 `status`（模块特有属性） |
| `actions` | object | 至少包含一个 action |

### 5.2 `@context` 标准格式

```json
"@context": [
  "https://www.w3.org/2022/wot/td/v1.1",
  {
    "soha": "https://soha.ai/vocab/v1",
    "saref": "https://saref.etsi.org/core/"
  }
]
```

### 5.3 安全方案

```json
"securityDefinitions": { "nosec_sc": { "scheme": "nosec" } },
"security": "nosec_sc"
```

---

## 6. moduleType 分类规则

| 类型 | 判定标准 | 示例 |
|------|----------|------|
| `actuator` | 产生物理运动（旋转、平移） | 关节、舵机、电机 |
| `peripheral` | 感知、输出或基础设施，不产生运动 | 电池、显示屏、按钮、交换机 |
| `kit` | 由多个子模组组成的复合设备 | （预留） |

**关键区分**：LED 灯带驱动虽然控制灯光输出，但不产生机械运动，属于 `peripheral`。

---

## 7. physics 填写指南

### 7.1 所有模组必填

```json
"soha:physics": {
  "joint": {
    "type": "<joint_type>"
  }
}
```

### 7.2 按 joint.type 分类

| joint.type | 含义 | 必填字段 | 适用模组 |
|------------|------|----------|----------|
| `revolute` | 有限角度旋转 | `axis`, `limits.lower`, `limits.upper`, `limits.maxVelocity`, `limits.maxEffort` | （预留） |
| `continuous` | 无限旋转 | `axis`, `limits.maxVelocity`, `limits.maxEffort` | 关节、舵机、高速动力 |
| `fixed` | 不运动 | 无额外字段 | 所有 peripheral |
| `prismatic` | 直线平移 | `axis`, `limits` | （预留） |

### 7.3 actuator 字段（仅 actuator 模组）

```json
"actuator": {
  "type": "<control_mode>",  // "position" | "velocity"
  "kp": 1000,                // 可选: P 增益
  "kv": 20                   // 可选: D 增益
}
```

| actuator.type | 含义 | 主 action |
|---------------|------|-----------|
| `position` | 闭环角度控制 | `setTarget(angle)` |
| `velocity` | 开环速度控制 | `setSpeed(speed)` |

---

## 8. Actions 设计规范

### 8.1 通用规则

- 每个 action 必须有 `forms`（至少一个 CoAP 绑定）
- 每个 action 的 `output` 必须包含 `success: boolean`
- 必须标注 `safe` 和 `idempotent`
- `description` 必须说明：阻塞/非阻塞行为、模式限制、副作用

### 8.2 output 规则

**标准 output**（命令类 action）：

```json
"output": {
  "type": "object",
  "properties": { "success": { "type": "boolean" } },
  "required": ["success"]
}
```

**扩展 output**（数据读取类 action）：

```json
"output": {
  "type": "object",
  "properties": {
    "success": { "type": "boolean" },
    "data": { "type": "array", "items": { "type": "integer" } }
  },
  "required": ["success"]
}
```

### 8.3 safe / idempotent 标注规则

| 标注 | 含义 | 示例 |
|------|------|------|
| `safe: true` | 不改变设备状态 | 读取传感器、查询状态 |
| `safe: false` | 改变设备状态 | 移动关节、设置颜色 |
| `idempotent: true` | 重复调用结果相同 | setMode("servo")、emergencyStop |
| `idempotent: false` | 重复调用可能有不同效果 | setTarget(angle)（追踪运动） |

### 8.4 actuator 专属

所有 `actuator` 模组**必须**提供 `emergencyStop` action：

```json
"emergencyStop": {
  "title": "Emergency stop",
  "description": "Immediately stop all movement.",
  "safe": true,
  "idempotent": true,
  "output": {
    "type": "object",
    "properties": { "success": { "type": "boolean" } },
    "required": ["success"]
  },
  "forms": [{ "href": "/actions/emergencyStop", "op": ["invokeaction"] }]
}
```

### 8.5 forms 格式

TD 中使用相对路径，运行时由 `base` 字段拼接完整 URL：

```json
"forms": [{ "href": "/actions/actionName", "op": ["invokeaction"] }]
```

---

## 9. Status 设计规范

所有模组的 `properties.status` 最终包含：

1. **来自基座 TM**（自动继承，TD 中不写）：`error`（integer）、`ready`（boolean）
2. **来自模块 TD**（模块特有）：各模块自定义的状态字段

TD 中只需写模块特有的 status 属性：

```json
"properties": {
  "status": {
    "type": "object", "readOnly": true, "observable": true,
    "properties": {
      "angle":    { "type": "number", "unit": "degree", "description": "Current joint angle" },
      "velocity": { "type": "number", "unit": "deg/s",  "description": "Current angular velocity" }
    },
    "forms": [{ "href": "/properties/status", "op": ["readproperty"] }]
  }
}
```

上位机合并后的完整 status 为：`{ error, ready, angle, velocity }`。

---

## 10. 发布前验证清单

- [ ] JSON 语法合法（`json.loads()` 无异常）
- [ ] TD 有 `id`、`base`、`security`、`securityDefinitions`
- [ ] TD 有 `links` 引用 `soha_base.tm.json`
- [ ] TD 的 `status.properties` 中**不含** `error` 和 `ready`（由基座 TM 提供）
- [ ] `@context` 包含 WoT TD v1.1 URL
- [ ] `@type` 为具体设备类型数组（非 `tm:ThingModel`）
- [ ] `soha:moduleType` 为 `actuator` / `peripheral` / `kit` 之一
- [ ] `soha:physics` 存在且 `joint.type` 正确
- [ ] 所有 action 有 `forms`
- [ ] 所有 action output 包含 `success: boolean`
- [ ] actuator 模组有 `emergencyStop` action
- [ ] README.md 已编写
