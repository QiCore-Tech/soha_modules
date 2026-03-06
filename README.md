# SOHA 模组注册仓库 (Module Registry)

SOHA 硬件模组的官方注册仓库。包含全局基座 Thing Model (TM) 和所有模组的 Thing Description (TD) 定义。

## 模组总览

| # | 目录 | 名称 | moduleType | TD ID |
|---|------|------|:----------:|-------|
| 1 | `soha_mod_joint` | 智能关节 | actuator | `SOHA-SJ-001` |
| 2 | `soha_mod_servo` | 智能微舵机 | actuator | `SOHA-SS-001` |
| 3 | `soha_mod_drive` | 高速动力 | actuator | `SOHA-HSM-001` |
| 4 | `soha_mod_battery` | 电池 | peripheral | `SOHA-BAT-001` |
| 5 | `soha_mod_switch` | 交换机 | peripheral | `SOHA-SW-001` |
| 6 | `soha_mod_developer` | 开发者 | peripheral | `SOHA-DEV-001` |
| 7 | `soha_mod_voice` | 语音 | peripheral | `SOHA-VOI-001` |
| 8 | `soha_mod_display` | 低分屏 | peripheral | `SOHA-DSP-001` |
| 9 | `soha_mod_led` | 灯光驱动 | peripheral | `SOHA-LED-001` |
| 10 | `soha_mod_controller` | 操控 | peripheral | `SOHA-CTL-001` |

## 架构

```
公网服务器 (soha.ai/tm/)           嵌入式设备
┌─────────────────────┐            ┌──────────────────────┐
│ soha_base.tm.json   │            │ soha_mod_xxx.td.json │
│ (error, ready)      │◀── GET ───│ (完整模块定义)        │── GET ──▶ 上位机
└─────────────────────┘            └──────────────────────┘
        │                                    │
        └──────── 上位机合并 ────────────────┘
                  = 完整交互模型
```

- **基座 TM**（`tm/soha_base.tm.json`）：定义所有模组共有的 `error` + `ready` 属性，托管在公网
- **模块 TD**（`soha_mod_xxxx.td.json`）：包含模块完整定义 + 实例参数，由设备直接 dump 返回

## 目录结构

```
soha_modules/
├── README.md                          # 本文件
├── STANDARD.md                        # 新模组开发标准
├── tm/
│   └── soha_base.tm.json             # 全局基座 TM（唯一）
├── vocab/
│   ├── soha-vocab-v1.jsonld           # SOHA JSON-LD 词汇定义
│   └── README.md                      # 词汇表说明
│
├── soha_mod_joint/                    # 智能关节（actuator）
│   ├── README.md
│   ├── soha_mod_joint.td.json         # Thing Description（完整定义）
│   └── mesh/                          # 3D 模型（预留）
│
├── soha_mod_servo/                    # 智能微舵机（actuator）
├── soha_mod_drive/                    # 高速动力（actuator）
├── soha_mod_battery/                  # 电池（peripheral）
├── soha_mod_switch/                   # 交换机（peripheral）
├── soha_mod_developer/                # 开发者（peripheral）
├── soha_mod_voice/                    # 语音（peripheral）
├── soha_mod_display/                  # 低分屏（peripheral）
├── soha_mod_led/                      # 灯光驱动（peripheral）
├── soha_mod_controller/               # 操控（peripheral）
│
└── _template/                         # 新模组开发模板
    ├── README.md.template
    └── module.td.json.template
```

## TM 与 TD 的关系

| | 基座 TM (`soha_base.tm.json`) | 模块 TD (`soha_mod_xxxx.td.json`) |
|---|---|---|
| 数量 | 全局唯一 1 个 | 每个模块 1 个 |
| 内容 | 公共属性（error, ready） | 模块完整定义 + 实例参数 |
| 存放位置 | 公网服务器 | 嵌入式设备 flash |
| `@type` | `"tm:ThingModel"` | 类型数组（如 `["saref:Actuator", "soha:ContinuousJoint"]`） |

TD 通过 `links` 引用基座 TM：

```json
"links": [{
  "rel": "type",
  "href": "https://soha.ai/tm/soha_base.tm.json",
  "type": "application/tm+json"
}]
```

## W3C WoT 合规性

所有模组 TD 符合 W3C WoT TD v1.1 标准：

- `@context` 包含 `https://www.w3.org/2022/wot/td/v1.1`
- TD 包含所有必填字段（`title`, `security`, `securityDefinitions`）
- 每个 affordance 都有 `forms`（CoAP 绑定）
- TD 通过 `links[rel=type]` 引用基座 TM
- SOHA 扩展字段通过标准 JSON-LD `@context` 机制声明（`soha:` 命名空间）

## SOHA 扩展词汇

通过 `soha:` 命名空间声明，标准 WoT 处理器可安全忽略：

| 字段 | 说明 |
|------|------|
| `soha:moduleType` | 模组分类：`actuator` / `peripheral` / `kit` |
| `soha:moduleName` | 模组全局唯一标识符（如 `"soha_joint_v1"`） |
| `soha:physics` | 物理模型（关节类型、轴向、限位、控制模式） |
| `soha:interfaceType` | 接口类型（仅 kit） |
| `soha:type` | 嵌入式数据类型提示 |

详见 `vocab/soha-vocab-v1.jsonld`。

## 开发新模组

1. 阅读 `STANDARD.md`
2. 复制 `_template/` 目录
3. 按标准填写 TD（引用基座 TM，只写模块特有定义）
4. 完成发布前验证清单

## 验证

```bash
# JSON 语法验证
python -c "
import json, glob
for f in sorted(glob.glob('soha_mod_*/*.json') + glob.glob('tm/*.json')):
    try:
        json.loads(open(f).read())
        print(f'OK  {f}')
    except Exception as e:
        print(f'ERR {f}: {e}')
"
```
