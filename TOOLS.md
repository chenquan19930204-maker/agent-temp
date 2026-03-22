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
| `get_token` | 获取认证 Token | - |
| `get_brand` | 获取品牌详情 | token |
| `get_daily_briefing` | 获取日报数据 | token, date(可选) |
| `list_apis` | 列出可用 API | category(可选) |
| `call_api` | 通用 API 调用 | token, path |

### 使用流程

```
1. get_token → 获取 token
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
{ "action": "get_daily_briefing", "token": "xxx" }

// 查询品牌详情
{ "action": "get_brand", "token": "xxx" }

// 列出所有关键词
{ "action": "call_api", "token": "xxx", "path": "/api/v1/keywords", "method": "GET" }
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

_Add whatever helps you do your job. This is your cheat sheet._
