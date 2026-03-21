# markdown-proxy

> 将任意 URL 转为干净的 Markdown，支持需要登录的页面（X/Twitter、微信公众号、飞书文档等）。

## 功能

给 Claude 发一个 URL，自动抓取完整内容并转为 Markdown。支持四种特殊平台的专用抓取：

| URL 类型 | 抓取方式 | 原因 |
|----------|---------|------|
| 微信公众号 (`mp.weixin.qq.com`) | 内置 Playwright 脚本 | 公众号有反爬，需无头浏览器 |
| 飞书文档 (`feishu.cn/docx/`, `/wiki/`, `/docs/`) | 内置飞书 API 脚本 | 需要 API 认证，自动转 Markdown |
| YouTube | 专用 YouTube skill | 视频内容有专用工具链 |
| 其他所有 URL | 代理级联：r.jina.ai → defuddle.md → agent-fetch | 免费、无需 API key |

## 前置条件

- [ ] 已安装 [Claude Code](https://docs.anthropic.com/en/docs/claude-code)
- [ ] **curl**（macOS/Linux 自带）
- [ ] （公众号抓取）Python 3.8+ 及 playwright
  ```bash
  pip install playwright beautifulsoup4 lxml
  playwright install chromium
  ```
- [ ] （飞书抓取）环境变量 `FEISHU_APP_ID` 和 `FEISHU_APP_SECRET`
  ```bash
  echo $FEISHU_APP_ID  # 验证已配置
  ```

## 安装

```bash
npx skills add joeseesun/markdown-proxy
```

验证：
```bash
ls ~/.claude/skills/markdown-proxy/SKILL.md
```

## 使用示例

直接给 Claude 发 URL：

- "帮我读一下这篇文章：https://example.com/post"
- "抓取这条推文：https://x.com/user/status/123456"
- "读一下这篇公众号：https://mp.weixin.qq.com/s/abc123"
- "把这个飞书文档转成 Markdown：https://xxx.feishu.cn/docx/xxxxxxxx"
- "读一下这个飞书知识库页面：https://xxx.feishu.cn/wiki/xxxxxxxx"

## 代理优先级

1. **r.jina.ai** — 内容最完整，保留图片链接
2. **defuddle.md** — 输出更干净，带 YAML frontmatter
3. **agent-fetch** — 本地工具，无需网络代理
4. **defuddle CLI** — 本地 CLI，适合普通网页

## 飞书文档支持

内置 `fetch_feishu.py` 脚本，通过飞书开放 API 抓取文档内容并自动转为 Markdown：

- 支持新版文档（docx）、旧版文档（doc）、知识库页面（wiki）
- 自动解析文档 blocks 并转换为 Markdown 格式
- 支持标题、列表、代码块、引用、待办、公式、图片等
- 需要飞书应用的 `FEISHU_APP_ID` 和 `FEISHU_APP_SECRET` 环境变量
- 应用需要 `docx:document:readonly` 权限

## 常见问题

| 问题 | 解决方法 |
|------|---------|
| 公众号抓取失败 | 运行 `playwright install chromium` 安装浏览器 |
| 飞书文档返回权限错误 | 检查 `FEISHU_APP_ID` 和 `FEISHU_APP_SECRET` 环境变量，确认应用有文档读取权限 |
| 飞书知识库页面抓取失败 | 确认应用有 `wiki:wiki:readonly` 权限 |
| r.jina.ai 返回空内容 | 自动降级到 defuddle.md（无需手动操作） |
| 所有代理都失败 | URL 可能有严格认证限制，尝试 `npx agent-fetch` |

## 致谢

- [r.jina.ai](https://r.jina.ai) — Jina AI 提供的免费 URL 转 Markdown 代理
- [defuddle.md](https://defuddle.md) — 干净的文章提取服务
- [Playwright](https://playwright.dev/) — 微信公众号抓取的浏览器自动化
- [飞书开放平台](https://open.feishu.cn/) — 飞书文档 API

## 关注作者

- **X (Twitter)**: [@vista8](https://x.com/vista8)
- **微信公众号「向阳乔木推荐看」**

<p align="center">
  <img src="https://github.com/joeseesun/terminal-boost/raw/main/assets/wechat-qr.jpg?raw=true" alt="向阳乔木推荐看公众号二维码" width="300">
</p>
