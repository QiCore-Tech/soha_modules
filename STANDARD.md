# SOHA 模组开发标准

本文档定义了开发新 SOHA 模组的规范和标准。所有新模组必须遵循此标准。

---

## 1. 命名规范

- 目录名：`soha_mod_xxxx`，全小写，下划线分隔
- TM 文件：`soha_mod_xxxx.tm.json`
- TD 文件：`soha_mod_xxxx.td.json`
- TM ID：`urn:soha:tm:soha_mod_xxxx`
- TD ID：`urn:dev:sn:SOHA-XXX-NNN`（XXX = 模组缩写，NNN = 序列号）

---

## 2. 目录结构

```
soha_mod_xxxx/
├── README.md                      # 模组介绍（中文）
├── soha_mod_xxxx.tm.json          # Thing Model（规范源）
├── soha_mod_xxxx.td.json          # Thing Description（示例实例）
└── mesh/                          # 3D 模型（预留）
    └── .gitkeep
```

---

## 3. TM 必填字段

### 3.1 顶层必填

| 字段 | 类型 | 说明 |
|------|------|------|
| `@context` | array | 必须含 `"https://www.w3.org/2022/wot/td/v1.1"` + `soha`/`saref` 命名空间 |
| `@type` | string | 必须为 `"tm:ThingModel"` |
| `title` | string | 英文人类可读名称 |
| `description` | string | 英文功能描述 |
| `soha:moduleType` | string | `"actuator"` / `"peripheral"` / `"kit"` |
| `soha:moduleName` | string | 模组全局唯一标识符（如 `"soha_joint_v1"`） |
| `soha:physics` | object | 物理模型（见 §5） |
| `properties` | object | 至少包含 `status` |
| `actions` | object | 至少包含一个 action |

### 3.2 TM 不应包含的字段

TM 是设备类型模板，**不应**包含实例信息：

- `id` — 具体设备序列号
- `base` — 具体 CoAP 端点
- `security` — 安全方案引用
- `securityDefinitions` — 安全方案定义

### 3.3 `@context` 标准格式

```json
"@context": [
  "https://www.w3.org/2022/wot/td/v1.1",
  {
    "soha": "https://soha.ai/vocab/v1",
    "saref": "https://saref.etsi.org/core/"
  }
]
```

---

## 4. moduleType 分类规则

| 类型 | 判定标准 | 示例 |
|------|----------|------|
| `actuator` | 产生物理运动（旋转、平移） | 关节、舵机、电机 |
| `peripheral` | 感知、输出或基础设施，不产生运动 | 电池、显示屏、按钮、交换机 |
| `kit` | 由多个子模组组成的复合设备 | （预留） |

**关键区分**：LED 灯带驱动虽然控制灯光输出，但不产生机械运动，属于 `peripheral`。

---

## 5. physics 填写指南

### 5.1 所有模组必填

```json
"soha:physics": {
  "joint": {
    "type": "<joint_type>"
  }
}
```

### 5.2 按 joint.type 分类

| joint.type | 含义 | 必填字段 | 适用模组 |
|------------|------|----------|----------|
| `revolute` | 有限角度旋转 | `axis`, `limits.lower`, `limits.upper`, `limits.maxVelocity`, `limits.maxEffort` | （预留） |
| `continuous` | 无限旋转 | `axis`, `limits.maxVelocity`, `limits.maxEffort` | 关节、舵机、高速动力 |
| `fixed` | 不运动 | 无额外字段 | 所有 peripheral |
| `prismatic` | 直线平移 | `axis`, `limits` | （预留） |

### 5.3 actuator 字段（仅 actuator 模组）

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

## 6. Actions 设计规范

### 6.1 通用规则

- 每个 action 必须有 `forms`（至少一个 CoAP 绑定）
- 每个 action 的 `output` 必须包含 `success: boolean`
- 必须标注 `safe` 和 `idempotent`
- `description` 必须说明：阻塞/非阻塞行为、模式限制、副作用

### 6.2 output 规则

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

### 6.3 safe / idempotent 标注规则

| 标注 | 含义 | 示例 |
|------|------|------|
| `safe: true` | 不改变设备状态 | 读取传感器、查询状态 |
| `safe: false` | 改变设备状态 | 移动关节、设置颜色 |
| `idempotent: true` | 重复调用结果相同 | setMode("servo")、emergencyStop |
| `idempotent: false` | 重复调用可能有不同效果 | setTarget(angle)（追踪运动） |

### 6.4 actuator 专属

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

### 6.5 forms 格式

TM 中使用相对路径（无 host）：

```json
"forms": [{ "href": "/actions/actionName", "op": ["invokeaction"] }]
```

TD 中通过 `base` 字段拼接完整 URL。

---

## 7. Status 设计规范

所有模组必须有 `properties.status`，且**必须**包含：

| 字段 | 类型 | 说明 |
|------|------|------|
| `error` | integer | 错误码，0 = 正常 |
| `ready` | boolean | 模组已初始化并可接受命令 |

```json
"status": {
  "type": "object",
  "readOnly": true,
  "observable": true,
  "properties": {
    "error": { "type": "integer", "description": "Error code. 0 = normal" },
    "ready": { "type": "boolean", "description": "Module initialized and ready for commands" }
  },
  "forms": [{ "href": "/properties/status", "op": ["readproperty"] }]
}
```

---

## 8. TD 实例化（从 TM 生成 TD）

将 TM 转换为 TD 的步骤：

1. **移除** `"@type": "tm:ThingModel"`
2. **添加** `@type` 为具体设备类型数组（如 `["saref:Actuator", "soha:RevoluteJoint"]`）
3. **添加** `id`：`"urn:dev:sn:SOHA-XXX-NNN"`
4. **添加** `base`：`"coap://<ip>:<port>"`
5. **添加** `securityDefinitions` 和 `security`：

```json
"securityDefinitions": { "nosec_sc": { "scheme": "nosec" } },
"security": "nosec_sc"
```

---

## 9. 发布前验证清单

- [ ] JSON 语法合法（`json.loads()` 无异常）
- [ ] TM 有 `"@type": "tm:ThingModel"`
- [ ] TM 无 `id`、`base`、`security`、`securityDefinitions`
- [ ] TD 有 `id`、`base`、`security`、`securityDefinitions`
- [ ] `@context` 包含 WoT TD v1.1 URL
- [ ] `soha:moduleType` 为 `actuator` / `peripheral` / `kit` 之一
- [ ] `soha:physics` 存在且 `joint.type` 正确
- [ ] `properties.status` 包含 `error` + `ready`
- [ ] 所有 action 有 `forms`
- [ ] 所有 action output 包含 `success: boolean`
- [ ] actuator 模组有 `emergencyStop` action
- [ ] README.md 已编写
