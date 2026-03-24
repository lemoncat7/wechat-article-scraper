# 微信公众号文章爬虫

纯 Python 标准库实现的微信公众号文章爬虫，无第三方依赖。

## 快速开始

### 一键运行

```bash
python3 ~/.openclaw/workspace/skills/wechat-article-scraper/scripts/run.py \
  --cookie "你的微信Cookie" \
  --biz "MzI1MTA4NzM0OQ==" \
  --name "公众号名称" \
  --output-dir ./output
```

### 分步运行

1. **获取文章列表**
```bash
python3 ~/.openclaw/workspace/skills/wechat-article-scraper/scripts/scraper.py \
  --cookie "你的微信Cookie" \
  --biz "MzI1MTA4NzM0OQ==" \
  --output ./articles.json
```

2. **生成报告**
```bash
python3 ~/.openclaw/workspace/skills/wechat-article-scraper/scripts/analyzer.py \
  --input ./articles.json \
  --name "公众号名称" \
  --output ./report.md
```

## 如何获取 Cookie 和 Biz

### 获取 Cookie（重要！）

微信 Cookie 必须从**手机微信 App**抓包获取，浏览器 Cookie 不生效。

**iOS 推荐工具**：Thor、Stream、mitmproxy
**Android 推荐工具**：HttpCanary、JustTrustMe、mitmproxy

抓包步骤：
1. 打开抓包工具，开始抓包
2. 在微信里打开任意公众号文章或进入公众号主页
3. 停止抓包，搜索 `mp.weixin.qq.com` 的请求
4. 复制请求头中的 `Cookie` 字段

**必需字段**：
- `appmsg_token`
- `pass_ticket`
- `wxuin`（微信 ID）
- `wxtokenkey`
- `eas_sid`
- `_qimei_h38`

### 获取 __biz

从文章链接中提取：
```
https://mp.weixin.qq.com/s?__biz=MzI1MTA4NzM0OQ==&mid=2650256145&...
                                     ^^^^^^^^^^^^^^^^
                                     这就是 biz
```

## 输出示例

### 文章 JSON
```json
[
  {
    "title": "【威博称重春节放假通知】",
    "date": "2026-02-01",
    "datetime": "2026-02-01T10:00:00",
    "year": 2026,
    "month": 2,
    "url": "http://mp.weixin.qq.com/s?...",
    "category": "放假通知"
  }
]
```

### 统计报告 (Markdown)
- 总文章数统计
- 年度发布趋势
- 内容分类统计（展会邀请、公司动态、放假通知等）
- 完整文章列表（按年月分组）

## 分类规则

| 分类 | 关键词 |
|------|--------|
| 展会/峰会邀请 | 展会、峰会、邀请、年会、博览会、论坛、大会 |
| 放假通知 | 放假、通知、春节、元旦、中秋、国庆 |
| 公司动态 | WEBOWT在、全球、注册、认证、销售 |
| 节日祝福 | 快乐、祝福、元宵、端午 |
| 活动回顾 | 回顾、精彩、圆满、成功 |
| 产品/技术 | 产品、技术、解决方案 |
| 其他 | 未匹配的文章 |

## 注意事项

1. **Cookie 有效期**：微信 Cookie 通常几天内有效，过期需重新获取
2. **频率限制**：请求间隔建议 >=0.5 秒，避免被封
3. **多图文**：自动拆分为独立条目统计
4. **去重规则**：根据标题+日期去重

## 文件结构

```
wechat-article-scraper/
├── SKILL.md          # 技能描述
├── README.md         # 本文件
└── scripts/
    ├── run.py        # 一键运行脚本
    ├── scraper.py    # 文章获取脚本
    └── analyzer.py   # 报告生成脚本
```
