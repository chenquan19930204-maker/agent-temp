# TOOLS.md - Local Notes

Skills define _how_ tools work. This file is for _your_ specifics — the stuff that's unique to your setup.

---

## GeoMan 数据工具

### 工具入口

| 工具 | 功能 |
|------|------|
| `geoman_data` | 统一入口，支持所有 GeoMan 数据操作 |

### 核心 Action

| Action | 功能 | 必填参数 |
|--------|------|----------|
| `get_brand` | 获取品牌详情 | - |
| `get_daily_briefing` | 获取日报数据 |, date(可选) |
| `list_apis` | 列出可用 API | category(可选) |
| `call_api` | 通用 API 调用 | path |

### 使用流程

```
2. get_brand / get_daily_briefing → 查询数据
3. 如需更多功能：list_apis → call_api
```

### 常用 API 路径

| 功能 | 路径 | 方法 |
|------|------|------|
| 日报 | /api/v1/monitors/daily-briefing | GET |
| 品牌详情 | /api/v1/brands/{brand_id} | GET |
| 关键词列表 | /api/v1/keywords | GET |
| 添加关键词 | /api/v1/keywords | POST |
| 文章列表 | /api/v1/articles/articles | GET |
| 生成文章 | /api/v1/articles/generate | POST |
| 竞品列表 | /api/v1/competitor-management/competitors | GET |
| 品牌分析 | /api/v1/agent/analyze | POST |

### 使用示例

```json
// 查询今日数据
{ "action": "get_daily_briefing" }

// 查询品牌详情
{ "action": "get_brand" }

// 列出所有关键词
{ "action": "call_api", "path": "/api/v1/keywords", "method": "GET" }
```

---

## GeoMan 消息工具

### 发送消息

```javascript
message({
  action: "send",
  channel: "geoman",
  target: "brandId",      // 客户 brandId
  message: "消息内容"
})
```

### 发送文件（文章/报告）

当需要发送文章、报告给客户确认时，使用文件发送：

```javascript
message({
  action: "send",
  channel: "geoman",
  target: "brandId",
  message: "请确认以下文章内容",
  media: "file:///path/to/article.md"  // 本地文件或 URL
})
```

**支持格式**：.md、.txt、.pdf 等

**自动处理**：
- 本地文件自动上传到 gm2oc OSS
- 生成可下载的附件链接

**使用场景**：
- 发送文章给客户确认
- 发送分析报告
- 发送策略文档

---

## Skills

Skills 在 `/skills/` 目录提供 GEO 优化能力：
- `geo-site-audit` — 技术 SEO 审计
- `geo-content-optimizer` — 内容优化
- `geo-schema-gen` — Schema 生成
- `geo-competitor-scanner` — 竞品分析
- 等等...

详细使用见各 skill 的 SKILL.md。

---

## Claude SEO 增强工具

> 通过 `sessions_spawn(runtime: "acp", agentId: "claude")` 调度 Claude Code 执行 Claude SEO 插件命令。

### 执行模板

```javascript
// 1. 创建输出目录（如尚未存在）
exec(command: "mkdir -p /tmp/seo-output && echo done")

// 2. 调度 Claude Code 执行 SEO 命令
sessions_spawn({
  runtime: "acp",
  agentId: "claude",
  cwd: "/tmp/seo-output",
  mode: "run",
  task: `
    请在 Claude Code 中运行以下 SEO 命令：
    /seo audit https://www.example.com/
    将完整报告保存到：/tmp/seo-output/seo-audit.md
    报告必须包含：SEO 健康分、技术SEO评分、内容评分、Schema状态、性能数据、问题列表、改进建议
  `
})

// 3. 等待约 5-6 分钟（完整审计需时）
// 监控进度：sessions_history(sessionKey, limit: 3)

// 4. 读取完整报告
read(path: "/tmp/seo-output/seo-audit.md")
```

### 查询进度

```javascript
// 通过 sessions_send 触发 Claude Code 响应
sessions_send({
  sessionKey: "agent:claude:acp:<uuid>",
  message: "请输出当前进度或已生成的报告内容",
  timeoutSeconds: 120
})

// 查看所有活跃 ACP session
sessions_list({ kinds: ["acp"], activeMinutes: 10, messageLimit: 3 })

// 读取 session 历史
sessions_history({ sessionKey: "agent:claude:acp:<uuid>", limit: 5 })
```

### 25 条命令详解

#### 核心分析（17条）

| 命令 | 输入 | 输出 | 典型耗时 |
|------|------|------|---------|
| `/seo audit <url>` | URL | 完整 SEO 报告（0-100分）+ 行动方案 | ~5min |
| `/seo page <url>` | URL | 单页面深度分析报告 | ~2min |
| `/seo technical <url>` | URL | 技术 SEO 报告（9类）| ~2min |
| `/seo content <url>` | URL | E-E-A-T 内容质量报告 | ~2min |
| `/seo schema <url>` | URL | Schema 检测/验证/生成报告 | ~1min |
| `/seo sitemap <url>` | URL | Sitemap 分析报告 | ~1min |
| `/seo sitemap generate` | — | 生成 XML Sitemap | ~1min |
| `/seo images <url>` | URL | 图片 SEO 报告 | ~1min |
| `/seo images optimize <url>` | URL | 图片优化建议 | ~1min |
| `/seo geo <url>` | URL | AI 搜索就绪度报告（GEO）| ~2min |
| `/seo backlinks <url>` | URL | 反向链接分析报告 | ~2min |
| `/seo local <url>` | URL | 本地 SEO 报告（GBP/引用/评论）| ~2min |
| `/seo maps [command] [args]` | args | 地图情报报告 | ~2min |
| `/seo hreflang <url>` | URL | 多语言 SEO 报告 | ~1min |
| `/seo google [command] [url]` | cmd+url | Google API 数据报告 | ~2min |
| `/seo cluster <seed-keyword>` | 关键词 | 语义聚类报告 + 内容架构 | ~3min |
| `/seo sxo <url> [keyword]` | URL+关键词 | 搜索体验优化报告 | ~2min |
| `/seo ecommerce <url>` | URL | 电商 SEO 报告 | ~2min |
| `/seo plan <business-type>` | 业务类型 | 战略 SEO 规划报告 | ~3min |

#### 漂移监控（3条）

| 命令 | 输入 | 输出 | 典型耗时 |
|------|------|------|---------|
| `/seo drift baseline <url>` | URL | 捕获基线快照（SQLite）| ~1min |
| `/seo drift compare <url>` | URL | 对比报告（变化列表）| ~1min |
| `/seo drift history <url>` | URL | 漂移历史记录 | ~1min |

#### 页面与内容（2条）

| 命令 | 输入 | 输出 | 典型耗时 |
|------|------|------|---------|
| `/seo programmatic [url or plan]` | URL/plan | 程序化 SEO 报告 | ~2min |
| `/seo competitor-pages [url or generate]` | URL/generate | 竞品对比页生成 | ~2min |

#### 扩展命令（需安装扩展）

| 命令 | 前提 | 功能 |
|------|------|------|
| `/seo firecrawl [command] <url>` | seo-firecrawl | 全站爬取 + URL 发现 |
| `/seo dataforseo [command]` | seo-dataforseo | Live SERP 数据 |
| `/seo image-gen <use-case> <description>` | seo-image-gen | AI 图片生成 |

### 文件落地规范

```
/tmp/seo-output/
├── seo-audit-<domain>.md        # 完整审计
├── seo-technical-<domain>.md    # 技术 SEO
├── seo-content-<domain>.md      # 内容质量
├── seo-schema-<domain>.md        # Schema 检查
├── seo-geo-<domain>.md           # GEO 报告
├── seo-sxo-<domain>.md           # SXO 报告
├── seo-drift-<domain>.md        # 漂移报告
├── seo-backlinks-<domain>.md    # 链接报告
├── seo-ecommerce-<domain>.md    # 电商报告
└── seo-competitor-<domain>.md   # 竞品报告
```

报告完成后，复制到 workspace 存档：
```bash
cp /tmp/seo-output/seo-audit.md ~/openclaw/workspace-geoman-brand/seo-reports/$(date +%Y-%m-%d)-audit.md
```

### 已知限制 & 应对

| 限制 | 说明 | 应对 |
|------|------|------|
| 子 agent 外部 API 失败 | ACP 中子 agent 无法访问外网（PageSpeed、CrUX 等）| 插件 fallback 到手动分析，仍能出报告 |
| 消息传输截断 | 长报告在 session 中被截断 | 必须用文件落地，不用 session 消息返回 |
| webchat 不支持 thread | 无法用 session 持久模式 | 用 `mode: "run"` + 文件落地 |
| 冷启动慢 | Claude Code 首次启动需 1-2 分钟 | 等待，不要重复调度 |

### 快速测试

验证调度是否正常：
```javascript
sessions_spawn({
  runtime: "acp",
  agentId: "claude",
  mode: "run",
  task: "请运行：/seo technical https://www.minimaxi.com/ 将结果保存到 /tmp/seo-output/test-technical.md"
})
```

---

_Add whatever helps you do your job. This is your cheat sheet._
