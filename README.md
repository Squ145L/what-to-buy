# Shopping Skill

按实际需求选购商品（全新或二手）：澄清需求 → 筛选候选 → 查型号问题 → 验证具体商品 → 给建议。

## 安装

```text
~/.codex/skills/shopping/SKILL.md
```

Windows 默认 `%USERPROFILE%\.codex\skills\shopping`；设了 `CODEX_HOME` 则用其下的 `skills\shopping`。

重新加载 Codex，新聊天里输入：

```text
$shopping 帮我找一台4000元以内的RTX 3060游戏本，用来打FPS。
```

## 依赖

搜索浏览用 Agent 现有工具，不需要额外安装程序。

`references/cdp-interface.md` 的接口取数需要 harness 提供带 CDP 的浏览器（Codex 内置浏览器可以）。没有就退回普通搜索浏览，skill 其余部分不受影响。
