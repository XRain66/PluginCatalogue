**English** | [中文](readme-zh_cn.md)

\>\>\> [Back to index](/readme.md)

## mg_events

### Basic Information

- Plugin ID: `mg_events`
- Plugin Name: MoreGameEvents
- Version: 0.1.0
  - Metadata version: 0.2.0
  - Release version: 0.1.0
- Total downloads: 6
- Authors: [Mooling0602](https://github.com/Mooling0602)
- Repository: https://github.com/Mooling0602/MoreGameEvents-MCDR
- Repository plugin page: https://github.com/Mooling0602/MoreGameEvents-MCDR/tree/main
- Labels: [`API`](/labels/api/readme.md)
- Description: Add more game events to MCDR.

### Dependencies

| Plugin ID | Requirement |
| --- | --- |
| [mcdreforged](https://github.com/Fallen-Breath/MCDReforged) | \>=2.1.0 |

### Requirements

| Python package | Requirement |
| --- | --- |

### Introduction

# MoreGameEvents-MCDR
向MCDR添加更多的游戏事件！

目前已经实现监听和派发死亡事件，后续将支持成就事件。

## 适用范围
各种Java版游戏服务端，非模组端支持最好
> 简中能跑的话，其他语言大概也没问题罢

README和文档部分默认不会支持简体中文和英文以外的其他语言，但欢迎PR

本插件理论上适合各种类型的服务端使用，包括带有各种第三方模组的服务器，只要服务端会在控制台严格按照指定的语言文件输出相关的事件信息。

注意：插件会和DieMessage及类似的BukkitAPI插件冲突，因为他们会修改服务端输出的死亡消息内容！

## 为什么需要此插件
- 为消息互通开发提供更丰富的游戏内事件信息
- 结合Minecraft Data API，为诸如“返回死亡点”等更丰富的玩法开发设计提供基础
- 其他（等待有缘人丰富此内容）

## 工作原理
将控制台输出的死亡、成就等提示消息，根据游戏语言文件进行解析，并派发成相关事件供下游插件处理。

因此，本插件并不能用于直接实现“死亡播报”等功能，需要配合下游插件使用。

考虑到多语言支持，插件并不会直接将识别到的info翻译成中文或类似操作，而是在派发的事件中提供以下内容：
- player 玩家名
- event 事件类型（直接使用翻译键名称即key）
- content 事件内容（字典）
- [1]content.lang 事件信息原始info输出的语言类型，你可以据此判断是否需要进行二次翻译
- content.raw 事件信息的原始info输出，如“Steve被僵尸杀死了”（虽然我觉得除了英文用户，一般用不到）
- content.advancement 成就名称（仅成就事件，暂未实现）
- content.death.killer 击杀者（杀死玩家的人或怪物），若没有则返回None
- content.death.weapon 击杀者使用的武器（参考上一条），若没有则返回None
> 目前插件仍处于早期开发（v0.x）阶段。针对以上内容，可能会随时做出修改，也欢迎各位提出建议！

然后，开发者可二次处理这些信息，并将其转发到需要的地方（例如消息互通转发到其他平台）或进行其他的插件开发。

只需要：
```python
from mcdreforged.api.all import *

def on_load(server: PluginServerInterface, prev_module):
    server.register_event_listener("PlayerDeathEvent", on_player_death)

def on_player_death(server: PluginServerInterface, player, event, content):
    player: str = player
    event: str = event # 翻译键名称
    killer: str = content.death.killer # 击杀者，玩家或怪物名称
    weapon: str = content.death.weapon # 击杀者所用武器
    # 由于几乎所有服务端都默认输出英文日志，因此需要进行二次开发，使用event对整个死亡消息进行翻译以及翻译killer（若击杀者为怪物）
    # 本人已开发了一个适用于此的插件，开源后将在下方给出链接以供参考
    # 链接：https://github.com/Mooling0602/DeathTips-MCDR
    # 你也可以自行处理这些

# 此部分暂未实现，仅供参考！
def on_player_advancement(server: PluginServerInterface, player, event, content):
    player: str = player
    advancement: str = content.advancement
```

### 备注
[1] 理论上这一部分可以支持输出中文log的服务端，但是这可能导致其他MCDR插件和MCDR本体无法正常解析服务器日志内容，故不推荐启用服务端的中文或其他语言log

## 语言文件要求及适配指南
- 仅对本插件而言，要求raw_lang（一般为英文，为服务端输出的所用的语言文件）
- 对据本插件进行了二次开发的插件的用户而言，要求raw_lang和tr_lang（用于翻出译文的语言文件，需和raw_lang严格对应），如果两个文件不相同的话
> tr_lang在下游插件使用
- 插件将原生支持Geyser互通服，如果检测到Geyser的语言文件路径，会自动使用（最开箱即用的一集）
- 服务端和其中安装的Mod应该含有raw_lang，你必须将这些分散的语言文件合成为一个，并存放在插件的配置目录中
- 客户端和其中安装的Mod应该同时含有raw_lang和tr_lang，你必须将分散的tr_lang合成为一个，并存放在下游插件的配置目录中
- 如果找不到tr_lang（例如Mod没有完成汉化等情况），你大概需要自行翻译或者放弃翻译
- 如果你运行的模组服，准备完这些，待下游插件开发完成后即可使用

### Download

> [!IMPORTANT]
> Read the README file in plugin repository before using it.

| File | Version | Upload Time (UTC) | Size | Downloads | Operations |
| --- | --- | --- | --- | --- | --- |
| [MoreGameEvents-v0.1.0.mcdr](https://github.com/Mooling0602/MoreGameEvents-MCDR/releases/tag/0.1.0) | 0.1.0 | 2024/11/30 10:32:24 | 198.2KB | 6 | [Download](https://github.com/Mooling0602/MoreGameEvents-MCDR/releases/download/0.1.0/MoreGameEvents-v0.1.0.mcdr) |

