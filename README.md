# Shopping Skill

帮助 Agent 按实际需求选购商品：理解需求、建立筛选标准、搜索候选、调查型号问题、验证具体商品并给出建议。适用于全新和二手商品。

本项目仅包含 Skill，搜索和浏览使用 Agent 当前可用的工具，无需安装专用采集程序。

## 在 Codex 中使用

将本仓库下载到 Codex 的 `skills/shopping` 目录，确保路径为：

```text
~/.codex/skills/shopping/SKILL.md
```

Windows 默认位置为 `%USERPROFILE%\.codex\skills\shopping`；设置了 `CODEX_HOME` 时，使用该目录下的 `skills\shopping`。

重新加载 Codex，在新聊天中输入：

```text
$shopping 帮我找一台4000元以内的RTX 3060游戏本，用来打FPS。
```

也可以用于吹风机、鼠标垫、电动牙刷等商品；判断重点由 Agent 根据品类和用途决定。
