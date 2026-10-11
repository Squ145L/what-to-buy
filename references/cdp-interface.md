# 从浏览器取列表数据

只讲机制，与站点无关。站点配方在 SKILL.md 的配方表里。

## 核心约束（两条路都适用）

- **大返回值会被截断**：JS 桥单次字符串返回的上限约 200,011 字符。超过就分块返回（每块 ≤ 50,000 再重组），或者干脆不返回——**原始响应留在脚本变量里，只把精简字段传出来**。
- 别用返回值大小赌运气：闲鱼列表接口一个 body 就有 302k 字符，整段返回必被截断。

## A. 走接口（首选）

标签页暴露 CDP：

```js
const cdp = await tab.capabilities.get('cdp');
cdp.documentation()          // 先读，命令名以实际返回为准
```

1. 调 `Network.enable`，然后 `tab.reload()`。
   **坑：首次/单独调用 `Network.enable` 可能出现 REPL 超时（60s/20s）；超时后先检查网络事件，可能已启用。**
   不要据此判定"不支持 CDP"——恢复句柄后其他命令都是秒级返回。
2. 分页读 `Network.responseReceived` / `Network.loadingFinished`，按 `response.url` 的 pathname 筛选目标接口。
3. 取响应体：

   ```js
   cdp.send('Network.getResponseBody', { requestId }, { timeoutMs: 8000 })
   ```

   返回 `{ body, base64Encoded }`；`base64Encoded` 为 false 时直接 `JSON.parse`。
4. 在脚本里解析，只把精简字段传出来。
5. 结果可能随重载变化：分批抓取不要混算，注明抓取时间。

## B. 退回渲染 DOM

`playwright.evaluate(() => document.documentElement.outerHTML)` 能取到，但要清楚那是**渲染后 DOM 的序列化**，不是服务器原始响应——要原始数据仍走 A。大页面按上面的核心约束分块返回。
