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

数据包内为 `pack.mcmeta` + `data/`，两种安装方式任选其一：

### 方式一：放入 `mods/` 文件夹（推荐，对所有存档生效）

将发布页的 **`create-connected-cdp-coloring-fix-1.0.0.zip`** 直接拖入 `.minecraft/mods/` 即可。
（Forge/NeoForge 会将含有合法 `pack.mcmeta` 的 zip 作为内置数据包加载。）

### 方式二：放入具体世界的 `datapacks/` 文件夹（仅对单个存档生效）

1. 解压 zip，得到 `create-connected-cdp-coloring-fix/` 文件夹（内含 `pack.mcmeta` 与 `data/`）。
2. 复制到 `saves/<你的世界名>/datapacks/create-connected-cdp-coloring-fix/`。
3. 进入游戏执行 `/reload`，或在世界设置中启用该数据包。

## 验证

1. 安装后进入世界，执行 `/reload`。
2. 放置一台鼓风机，气流方向末端放一个 `create_connected` 的染色触媒方块（如绿色染色触媒）。
3. 启动鼓风机，应在触媒处看到对应颜色的染色粒子。
4. 在气流通道中放入可染色物品（如 CDP 批量染色配方所需的物品），即可被批量染色。

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
