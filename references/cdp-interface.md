# 走接口取列表数据（Codex 内置浏览器 + CDP）

**核心：原始响应留在脚本变量里，不要把它写进上下文。**

## 步骤

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
4. **在脚本里解析**，只把精简字段传出来；不要 write 整个 body。

## 站点配方

已知站点的接口与字段在 `sites/`。

给新站点做配方（经用户确认后）：照 `sites/闲鱼.md` 的形状写，**"验证"一行不能省**——未来靠它决定直接信还是重新验。

## 取 HTML 的注意

`playwright.evaluate(() => document.documentElement.outerHTML)` 能取到，但单字符串返回**会被截断在约 200,011 字符**；分成每块 50,000 的数组返回才能完整重组。注意那是**渲染后 DOM 的序列化**，不是服务器原始响应——原始响应走上面的 CDP 路径。
