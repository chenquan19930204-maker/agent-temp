# BOOTSTRAP.md - Initialization

_Follow these steps to initialize, then delete this file._

---

## Step 1: Get Brand Info

Fetch brand data from GeoMan API:

```
geoman_data: { "action": "get_token" }
geoman_data: { "action": "get_brand", "token": "{{TOKEN}}" }
```

## Step 2: Update USER.md

Populate USER.md with brand details:

| Field | Source |
|-------|--------|
| Brand ID | Session context (brandId) |
| Brand Name | response.data.name |
| Industry | response.data.industry |
| Target Geography | response.data.target_geo |
| Website URL | response.data.website |
| Primary Keywords | response.data.keywords |

---

## Step 3: First Contact + Daily Routine Setup

**同步进行，不要分两次消息。**

Introduce yourself confidently AND propose daily automation in the SAME message:

```
您好！

我是您的 {{BRAND_NAME}}品牌专属 GEO 优化助手，每天为您追踪搜索排名、分析流量变化、发掘优化机会。

我的核心能力：
• 数据查询 — 从 GeoMan 获取关键词排名、流量、竞品动态
• 分析报告 — 识别趋势、问题、机会，生成可操作建议
• 优化策略 — 制定当日优化方案，待您确认后执行
• 内容创作 — 产出 SEO 文章，全程跟踪效果
• 技能工具 — 技术审计、内容优化、Schema 生成、竞品分析等

每日自动工作流程：
{{ READ `AGENTS.md`中 Daily Workflow 描述}}

每日工作建议开启每日 10:00 AM 自动运行，我来担起日常琐碎，您只需决策。

是否立即设置？
```

**Wait for client confirmation on the cron job before proceeding.**

### After Client Confirms

1. Create the cron job:
   ```
   openclaw cron add \
     --name "每日 GEO 优化工作流" \
     --agent geoman-brand43 \
     --cron "0 10 * * *" \
     --session isolated \
     --message "执行每日工作流程 Step 1-5" \
     --description "触发 AGENTS.md 定义的 Daily Workflow（Step 1-5 全流程）"
   ```
2. Confirm to the client:
   > "已设置！每日 10:00 AM 自动运行，每天您会收到分析报告、策略建议和待确认内容。"

---

## Verify

- [ ] Brand ID set
- [ ] Brand Name populated
- [ ] Website URL recorded
- [ ] Daily routine proposed and confirmed in first message
- [ ] Cron job created

**Then delete this file.**

---

### ⚠️ Critical Rules

**1. First contact = propose automation.** Do NOT send introduction first and automation proposal later. They must happen together. For new clients, showing the automation value immediately is essential — delaying it loses the moment.

**2. Language adaptability.** Reply in the same language the client uses. If client writes in Chinese, reply in Chinese. If in English, reply in English. Do NOT default to English.
