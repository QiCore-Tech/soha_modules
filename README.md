# SOHA 模组注册仓库 (Module Registry)

SOHA 硬件模组的官方注册仓库。包含所有模组的 Thing Model (TM) 和 Thing Description (TD) 定义。

## 模组总览

| # | 目录 | 名称 | moduleType | TM ID | 示例 TD ID |
|---|------|------|:----------:|-------|-----------|
| 1 | `soha_mod_joint` | 智能关节 | actuator | `urn:soha:tm:soha_mod_joint` | `SOHA-SJ-001` |
| 2 | `soha_mod_servo` | 智能微舵机 | actuator | `urn:soha:tm:soha_mod_servo` | `SOHA-SS-001` |
| 3 | `soha_mod_drive` | 高速动力 | actuator | `urn:soha:tm:soha_mod_drive` | `SOHA-HSM-001` |
| 4 | `soha_mod_battery` | 电池 | peripheral | `urn:soha:tm:soha_mod_battery` | `SOHA-BAT-001` |
| 5 | `soha_mod_switch` | 交换机 | peripheral | `urn:soha:tm:soha_mod_switch` | `SOHA-SW-001` |
| 6 | `soha_mod_developer` | 开发者 | peripheral | `urn:soha:tm:soha_mod_developer` | `SOHA-DEV-001` |
| 7 | `soha_mod_voice` | 语音 | peripheral | `urn:soha:tm:soha_mod_voice` | `SOHA-VOI-001` |
| 8 | `soha_mod_display` | 低分屏 | peripheral | `urn:soha:tm:soha_mod_display` | `SOHA-DSP-001` |
| 9 | `soha_mod_led` | 灯光驱动 | peripheral | `urn:soha:tm:soha_mod_led` | `SOHA-LED-001` |
| 10 | `soha_mod_controller` | 操控 | peripheral | `urn:soha:tm:soha_mod_controller` | `SOHA-CTL-001` |

## 目录结构

```
soha_modules/
├── README.md                          # 本文件
├── STANDARD.md                        # 新模组开发标准
├── vocab/
│   ├── soha-vocab-v1.jsonld           # SOHA JSON-LD 词汇定义
│   └── README.md                      # 词汇表说明
│
├── soha_mod_joint/                    # 智能关节（actuator）
│   ├── README.md
│   ├── soha_mod_joint.tm.json         # Thing Model（规范源）
│   ├── soha_mod_joint.td.json         # Thing Description（示例实例）
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
    ├── module.tm.json.template
    └── module.td.json.template
```

## TM vs TD

| | Thing Model (TM) | Thing Description (TD) |
|---|---|---|
| 用途 | 设备类型模板（规范源） | 已部署的具体设备实例 |
| `@type` | `"tm:ThingModel"` | 类型数组（如 `["saref:Actuator", "soha:RevoluteJoint"]`） |
| `id` | 无 | 有（`urn:dev:sn:SOHA-XXX-NNN`） |
| `base` | 无 | 有（`coap://IP:PORT`） |
| `security` | 无 | 有（`"nosec_sc"`） |
| 文件后缀 | `.tm.json` | `.td.json` |

## W3C WoT 合规性

所有模组 TD/TM 符合 W3C WoT TD v1.1 标准：

- `@context` 包含 `https://www.w3.org/2022/wot/td/v1.1`
- TD 包含所有必填字段（`title`, `security`, `securityDefinitions`）
- 每个 affordance 都有 `forms`（CoAP 绑定）
- SOHA 扩展字段通过标准 JSON-LD `@context` 机制声明（`soha:` 命名空间）

## SOHA 扩展词汇

通过 `soha:` 命名空间声明，标准 WoT 处理器可安全忽略：

| 字段 | 说明 |
|------|------|
| `soha:moduleType` | 模组分类：`actuator` / `peripheral` / `kit` |
| `soha:physics` | 物理模型（关节类型、轴向、限位、控制模式） |
| `soha:interfaceType` | 接口类型（仅 kit） |
| `soha:type` | 嵌入式数据类型提示 |

详见 `vocab/soha-vocab-v1.jsonld`。

## 开发新模组

1. 阅读 `STANDARD.md`
2. 复制 `_template/` 目录
3. 按标准填写 TM → 从 TM 派生 TD
4. 完成发布前验证清单

## 验证

```bash
# JSON 语法验证
python -c "
import json, glob
for f in sorted(glob.glob('soha_mod_*/*.json')):
    try:
        json.loads(open(f).read())
        print(f'✅ {f}')
    except Exception as e:
        print(f'❌ {f}: {e}')
"
```
