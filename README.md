# 铁傀儡号角 · Golem Horn

一个 Minecraft Java 版 **1.21.11 / Fabric** 模组：吹响山羊角，召唤一只为你战斗的铁傀儡。

## 功能

- **右键**：召唤一只守卫铁傀儡，替你和它自己战斗，**冷却 30 秒**（物品栏上会显示冷却）。
- **Shift + 右键**：打开设置界面。
  - **登场方式**：`从天而降`（从高空砸下，落地有铁砧音效与烟尘）或 `破土而出`（像监守者一样从地下缓缓爬出，带监守者的破土音效与粒子）。
  - **白名单**（永不攻击）：例如 `minecraft:villager`、`minecraft:wolf`、`minecraft:player`。白名单优先级最高。
  - **攻击目标**：显式指定要攻击的生物，例如 `minecraft:creeper`。可以添加玩家。
  - 列表条目**点击即可移除**，输入框支持 `minecraft:zombie` 这种完整 ID，也支持省略命名空间（例如 `zombie`）。
- 设置保存在**号角物品本身**（数据组件）里：换手、丢给朋友、放进箱子都不会丢；同一个号角被复制后设置也会跟随。
- 铁傀儡会**跟随主人**、保护主人；默认（攻击目标为空时）主动攻击敌对生物，并反击攻击主人的生物。
- 白名单优先于一切：即使生物在攻击目标里，只要在白名单中就不会被攻击。
- 召唤出的铁傀儡不会自然消失，死亡后正常掉落铁锭。

## 合成

无序合成：**1 个山羊角 + 1 个铁块 → 铁傀儡号角**。

也可以在创造模式物品栏的「工具与实用物品」页找到它。

## 安装

1. 安装 **Fabric Loader 0.19.5 或更高版本**（推荐 0.20.1）。
2. 把 [Fabric API](https://modrinth.com/mod/fabric-api) `0.141.6+1.21.11` 或更新版本放进 `mods/`。
3. 把 `golemhorn-1.0.0.jar` 放进 `.minecraft/mods/`。
4. 需要 **Java 21+**（游戏本体要求）与 Minecraft **1.21.11**。

## 从源码构建

构建脚本使用 [Fabric 官方模板](https://github.com/FabricMC/fabric-example-mod)，需要：

- JDK **25**（Fabric Loom 1.19 运行构建需要 Java 25；产物仍以 Java 21 为目标）
- 能够访问 Maven Central、Fabric Maven 与 Mojang 资源站

```bash
./gradlew build
```

产物位于 `build/libs/golemhorn-1.0.0.jar`。

## 代码结构

| 文件 | 作用 |
| --- | --- |
| `GolemHornMod` | 入口：注册组件、物品、实体、网络包与创造模式物品栏 |
| `HornSettings` | 设置数据模型（登场方式 + 白名单 + 攻击目标），带 Codec 与网络 StreamCodec |
| `ModComponents` | 把 `HornSettings` 注册为物品数据组件，并同步到客户端 |
| `GolemHornItem` | 右键召唤 / Shift+右键发设置包 / 30 秒冷却 / 悬浮提示 |
| `GuardianGolem` | 铁傀儡子类：主人、跟随、目标筛选、破土/坠落登场动画、存档 |
| `net/*` | `OpenHornConfigPayload`（S2C）与 `SaveHornConfigPayload`（C2S） |
| `client/HornConfigScreen` | 设置界面（模式切换、两个列表、翻页、添加/移除） |
| `client/GolemHornClient` | 注册铁傀儡渲染器并打开设置界面 |

## 说明

- 破土而出使用的是监守者的 `warden_emerge` 音效与粒子，配合逐帧抬升的实体位置来模拟「从地里爬出」。
- 因为铁傀儡使用自定义实体，服务端与客户端都要安装本模组。
