---
name: wechat-article-scraper
description: 微信公众号文章爬虫工具 - 使用微信 Cookie 批量抓取公众号文章列表、自动分类、生成统计报告。支持文章列表获取、文章详情抓取、分类统计。
metadata:
  {
    "openclaw": {
      "requires": { "bins": ["python3"] },
      "install": []
    }
  }
---

# 微信公众号文章爬虫技能

使用微信 Cookie 抓取公众号文章列表，并进行统计分析。

## 使用场景

当用户说：
- "抓取公众号文章"
- "爬取微信公众号"
- "统计公众号"
- "分析公众号文章"
- "导出公众号文章"

## 前置要求

需要从微信 App（手机抓包）获取有效 Cookie：

1. **Cookie 必需字段**：
   - `appmsg_token`
   - `pass_ticket`
   - `wxuin`（微信 ID）
   - `wxtokenkey`
   - `eas_sid`
   - `_qimei_h38` 等

2. **__biz 参数**：公众号唯一标识，从文章链接中提取
   - 格式：`https://mp.weixin.qq.com/s?__biz=MzI1MTA4NzM0OQ==&...`

3. **Cookie 获取方式**：
   - iOS：Thor/Stream 等抓包工具
   - Android：HttpCanary/JustTrustMe 等
   - 电脑微信：F12 开发者工具 Network 面板

## 核心脚本

### 1. 批量获取文章列表

```bash
python3 ~/.openclaw/workspace/skills/wechat-article-scraper/scripts/scraper.py \
  --cookie "appmsg_token=xxx;pass_ticket=xxx;..." \
  --biz "MzI1MTA4NzM0OQ==" \
  --output ./articles.json
```

参数：
- `--cookie`：微信 Cookie 字符串（必需）
- `--biz`：公众号 __biz 参数（必需）
- `--output`：输出 JSON 文件路径（默认 ./articles.json）
- `--all`：获取全部文章（默认 True）
- `--offset`：分页偏移量，默认 0
- `--count`：每次请求数量，默认 10

### 2. 生成统计报告

```bash
python3 ~/.openclaw/workspace/skills/wechat-article-scraper/scripts/analyzer.py \
  --input ./articles.json \
  --output ./report.md
```

参数：
- `--input`：文章 JSON 文件（必需）
- `--output`：输出 Markdown 报告路径

### 3. 一键完成（获取+分析+报告）

```bash
python3 ~/.openclaw/workspace/skills/wechat-article-scraper/scripts/run.py \
  --cookie "你的Cookie" \
  --biz "公众号biz" \
  --name "公众号名称"
```

输出：
- `{name}_articles.json`：原始文章数据
- `{name}_report.md`：统计报告

## 分类规则

自动根据标题关键词分类：

| 分类 | 关键词 |
|------|--------|
| 展会/峰会邀请 | 展会、峰会、邀请、年会、博览会、论坛、大会、药机、制药 |
| 放假通知 | 放假、通知、春节、元旦、中秋、国庆、圣诞 |
| 公司动态 | WEBOWT在、全球、注册、认证、销售、称重 |
| 节日祝福 | 快乐、祝福、元宵、端午、月圆 |
| 活动回顾 | 回顾、精彩、圆满、成功、感谢、共赴 |
| 产品/技术 | 产品、技术、解决方案、系统、设备 |
| 其他 | 未匹配到以上分类的文章 |

## 输出示例

### 统计报告结构

```markdown
# {公众号名称} 公众号文章统计报告

## 总览
- 总文章数
- 统计年份范围

## 年度发布统计
按年份统计文章数量和占比

## 分类统计
按内容分类统计文章数量和占比

## 完整文章列表
按年月分组展示所有文章
```

## 注意事项

1. **Cookie 有效期**：微信 Cookie 通常几天内有效，过期需要重新获取
2. **频率限制**：请求间隔建议 >500ms，避免触发限制
3. **多图文文章**：自动拆分为独立条目统计
4. **去重规则**：根据标题+日期去重

## 常见问题

**Q: 报 retkey: 11 错误？**
A: Cookie 已过期，需要重新抓取

**Q: 报"请在微信客户端打开链接"？**
A: Cookie 不是从微信 App 获取的，必须用手机微信抓包

**Q: 怎么获取 __biz？**
A: 从文章链接中提取 `__biz=` 后面的 Base64 字符串
