# Create: Connected × Create: Dragons Plus 批量染色兼容修复包

一个纯数据包（Data Pack）修复，解决 **Create: Connected 1.2.3** 的染色触媒（Fan Dyeing Catalyst）在
**Create: Dragons Plus 1.11.9** 的鼓风机（Blower / Fan）中无法触发批量染色配方的问题。

## 适用版本

| 项目 | 版本 |
| --- | --- |
| Minecraft | **1.20.1** |
| Create: Connected | **1.2.3** |
| Create: Dragons Plus | **1.11.9** |
| 数据包 pack_format | **15**（对应 MC 1.20.1） |

> 该修复仅针对上方组合。Create: Connected **1.3.0+** 已原生修复此兼容问题，若可升级请直接升级；
> 若因存档/整合包限制停留在 1.2.3，请使用本修复包。

## 问题说明（根因）

Create: Dragons Plus 的「批量染色（Bulk Coloring）」配方由 `ColoringFanProcessingType` 实现，其判定逻辑为：
当鼓风机气流末端所在的方块位于标签 `create_dragons_plus:fan_processing_catalysts/coloring/<颜色>` 中时，
才视为有效的着色催化剂并施加染色。

然而在 Create: Connected 1.2.3 中，染色触媒方块 `create_connected:<颜色>_fan_dyeing_catalyst`
**没有被注册进上述 CDP 的着色催化剂方块标签**（该标签在 CDP 中默认为空，依赖联动模组补充）。
结果：CDP 不认识 create_connected 的染色触媒 → 鼓风机吹不出染色粒子、批量染色配方无法触发。

## 修复原理

本数据包为 16 种颜色分别写入标签文件：

```
data/create_dragons_plus/tags/blocks/fan_processing_catalysts/coloring/<颜色>.json
```

内容为将对应方块加入标签，例如绿色：

```json
{ "values": ["create_connected:green_fan_dyeing_catalyst"] }
```

覆盖全部 16 种染料颜色：
`black, blue, brown, cyan, gray, green, light_blue, light_gray, lime, magenta, orange, pink, purple, red, white, yellow`。

使 CDP 正确识别 create_connected 的染色触媒，从而正常产生染色粒子并触发批量染色配方。

## 安装方法

数据包内为 `pack.mcmeta` + `data/`。**推荐放进具体世界的 `datapacks/` 文件夹**（对单存档生效，最稳妥）：

1. 解压发布页的 zip，得到 `create-connected-cdp-coloring-fix/` 文件夹（内含 `pack.mcmeta` 与 `data/`）。
2. 复制到 `saves/<你的世界名>/datapacks/create-connected-cdp-coloring-fix/`。
3. **完全重启世界**（仅 `/reload` 有时不足以重算标签），进入后 `/datapack list enabled` 应能看到该包。

也可以直接把 **`create-connected-cdp-coloring-fix-1.0.0.zip`** 丢进 `saves/<世界名>/datapacks/`（MC 认 zip）。

> 注意：部分 Forge 版本**不会**把放在 `mods/` 里的普通 zip 当数据包加载，故不建议放 `mods/`。

> ⚠️ 打包注意：本 zip 已使用正斜杠 `/` 规范打包。若自行重新打包，**不要**用 Windows PowerShell 5.1 的 `Compress-Archive`——它会写入反斜杠 `\` 分隔符，导致 MC 能识别数据包（显示已启用）却读不到 `data/` 内的文件，表现为「已启用但完全无效」。

## 验证

1. 安装后重启世界，执行 `/reload`。
2. **先确认标签是否生效**：站在触媒方块上执行
   `/execute if block ~ ~ ~ #create_dragons_plus:fan_processing_catalysts/coloring/green run say TAG_OK`
   出现 `TAG_OK` 即表示标签已加载。
3. 放置鼓风机，气流方向末端放一个 `create_connected` 的染色触媒方块（如绿色染色触媒），启动鼓风机：
   **气流本身应被染成对应颜色**——这是着色生效最直观的信号。
4. 用**传送带**让可染色物品穿过气流即可被批量染色（直接丢在地上会掉出气流，看不出效果）。
   染色粉尘粒子为低概率生成，没有粒子不代表未生效，以气流颜色为准。

## 文件结构

```
create-connected-cdp-coloring-fix/
├── pack.mcmeta
├── README.md
└── data/
    └── create_dragons_plus/
        └── tags/
            └── blocks/
                └── fan_processing_catalysts/
                    └── coloring/
                        ├── black.json
                        ├── blue.json
                        ├── ... (16 种颜色)
                        └── yellow.json
```

## 备注

- 本修复包不含任何代码，纯标签数据，安全可卸载（移除 zip / 数据包后 `/reload` 即失效）。
- 上游 Create: Connected 1.3.0 已修复该兼容性问题；本包仅作为停留在 1.2.3 时的临时方案。
