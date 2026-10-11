# What To Buy（买啥？）

帮你决定买什么、为什么值得买、哪些不值得买。适用于全新和二手商品。

从用途出发筛选候选，调查型号的已知问题，再核对具体商品的配置、价格和状态。比较时说明便宜一档牺牲什么、贵一档增加什么，也可以建议等待或不买。

## 工作方式

理解需求 → 定标准 → 搜候选 → 查型号问题 → 验证具体商品 → 给建议。

两条值得先知道的：**标准随品类和用途变化，不套固定参数清单**；**横评展示主要候选、型号问题和排除理由，结论先行**。

## 安装

最省事的办法——把这句话发给 Codex，它自己装：

> 读 https://github.com/Squ145L/what-to-buy 的 README，帮我把这个 skill 装好。

它照做时需要知道的：

1. 用 clone 或原始文件读 `SKILL.md`，**不要用网页摘要工具**——经过总结会失真。
2. 只把 `SKILL.md` 和整个 `references/` 放到 `~/.codex/skills/what-to-buy/`。
3. **`docs/` 不要装**：里面的 `evaluation.md` 写着测试用的预期行为，放进 skill 目录会污染真实运行。
4. 装完重新加载 Codex。

手动装也一样，只要 `SKILL.md` 和整个 `references/`：

```text
~/.codex/skills/what-to-buy/
├── SKILL.md
└── references/
    ├── cdp-interface.md
    └── sites/
        ├── 闲鱼.md
        └── 京东.md
```

Windows 默认 `%USERPROFILE%\.codex\skills\what-to-buy`；设了 `CODEX_HOME` 则用其下的 `skills\what-to-buy`。

**不要只复制 `SKILL.md`**，站点配方要一起装。装完重新加载 Codex，在新聊天里使用。

## 使用

直接描述需求，也可以用 `$what-to-buy` 明确调用：

```text
$what-to-buy 帮我找4000以下用于打CS2的二手游戏本，闲鱼买。
```

```text
$what-to-buy 300元左右买个通勤用的无线降噪耳机，优先京东。
```

```text
$what-to-buy 帮我比较这两个商品链接，哪个更值？
```

可以补充预算是否严格、用途、购买平台、新旧要求，或直接提供商品链接。

## 站点与工具

| 站点 | 配方与实测范围 |
| --- | --- |
| 闲鱼 | [配方](references/sites/闲鱼.md)：已验证登录后的列表接口与价格解析，需要登录。 |
| 京东 | [配方](references/sites/京东.md)：已验证登录后的30条列表、商品详情与配置切换；未验证翻页和结算。 |
| 淘宝、拼多多 | 暂无配方。2026-10-11 本次 Codex 浏览器测试被站点安全策略拦截，未完成采集验证。 |

接口取数需要浏览器提供 CDP 能力，不需要额外安装爬虫或服务；没有该能力时使用普通搜索浏览。**配方是取数方法，不是平台可用性的保证**——登录状态、页面变化和浏览器工具权限都会影响实际访问；没有配方的站点，在工具允许访问时照常浏览。

## 仓库内容

- [SKILL.md](SKILL.md)：购物决策流程。
- [接口取数说明](references/cdp-interface.md)：采集机制与回退方法。
- `references/sites/`：站点字段、价格处理与验证记录。
- [评估场景](docs/evaluation.md)：简单比较、全新选购和二手商品的测试方法。
