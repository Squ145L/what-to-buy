# 走接口取列表数据（Codex 内置浏览器 + CDP）

读渲染后的页面会带进大量噪音。走接口 + 脚本解析，实测能把同样 30 条数据的字符数从 25,878 降到 1,234（约 −95%）。

**核心：原始响应留在脚本变量里，不要把它写进上下文。**

## 步骤

标签页暴露 CDP：

```js
const cdp = await tab.capabilities.get('cdp');
cdp.documentation()          // 先读，命令名以实际返回为准
```

1. 调 `Network.enable`，然后 `tab.reload()`。
   **坑：首次/单独调用 `Network.enable` 会出现 REPL 超时（60s/20s），但它其实已经启用。**
   不要据此判定"不支持 CDP"——恢复句柄后其他命令都是秒级返回。
2. 分页读 `Network.responseReceived` / `Network.loadingFinished`，按 `response.url` 的 pathname 筛选目标接口。
3. 取响应体：

   ```js
   cdp.send('Network.getResponseBody', { requestId }, { timeoutMs: 8000 })
   ```

   返回 `{ body, base64Encoded }`；`base64Encoded` 为 false 时直接 `JSON.parse`。
4. **在脚本里解析**，只把精简字段传出来；不要 write 整个 body。

## 闲鱼搜索（实测 2026-10-10）

- 接口：`/h5/mtop.taobao.idlemtopsearch.pc.search/1.0/`，状态 200，body 约 302k 字符
- `ret` = `["SUCCESS::调用成功"]`
- 列表在 **`data.resultList`**（不是 `itemList`），单页 30 条
- 每条在 `r.data.item.main.exContent`，含 `itemId` / `title` / `area` / `price`
- **`price` 是样式数组**：过滤掉 `type === 'sign'` 的段，拼 `text` 再转数值
- 校验过：30 条 ID/price 与同次 DOM 里的 30 条完全一致

## 上下文对比

| 取法 | 字符数 |
|---|---|
| 整页 `domSnapshot()` | 25,878 |
| 30 条 `{id,price,description}` | 5,701 |
| 加 `area` | 6,062 |
| 按条件筛出的 7 条 | **1,234** |

## 取 HTML 的注意

`playwright.evaluate(() => document.documentElement.outerHTML)` 能取到，但单字符串返回**会被截断在约 200,011 字符**；分成每块 50,000 的数组返回才能完整重组。注意那是**渲染后 DOM 的序列化**，不是服务器原始响应——原始响应走上面的 CDP 路径。

---

本文是 Codex 内置浏览器的做法。换 harness 时保留"**走接口 + 脚本解析**"这一条原则即可。
