---
when_to_use: 微信读书/评分/书架/阅读统计/笔记划线/点评/推荐/书籍搜索；Calibre 书库评分（rating 计算）
do_not_use: 无微信读书需求时；仅豆瓣即可满足的元数据查询
version: 1.0.0
last_updated: 2026-08-05
---
# 微信读书 — Skill 能力手册（weread-skills）

本文件是《calibre书库管理.md》的配套文件，包含两大部分：

1. **书库评分数据源**（`rating` 字段按本文件计算，第 5-7 节）；
2. **weread-skills 全部能力与用法示例**（第 1-4 节，可用于搜索、书架、阅读统计、笔记、点评、推荐等）。

## 1. 能力总览

| 能力 | 接口 | 说明 |
|---|---|---|
| 搜索书籍 | `/store/search` | 多类型搜索（scope 切换），返回评分/评分人数/在读人数 |
| 书籍信息 | `/book/info` | 详情：作者/译者/出版社/ISBN/简介/评分/字数 |
| 章节目录 | `/book/chapterinfo` | 目录层级、章节字数、付费状态 |
| 阅读进度 | `/book/getprogress` | 百分比进度 + 累计阅读时长 |
| 书架管理 | `/shelf/sync` | 你的书架（电子书 + 有声书/专辑分开）|
| 阅读统计 | `/readdata/detail` | 阅读时长/天数/排行/偏好（weekly/monthly/annually/overall）|
| 笔记划线 | `/user/notebooks` `/book/bookmarklist` `/review/list/mine` `/book/underlines` `/book/bestbookmarks` | 个人划线/想法/书签统计与导出；章节热门划线 |
| 书籍点评 | `/review/list` | 书籍公开点评（推荐/不行/最新/一般筛选）|
| 推荐好书 | `/book/recommend` `/book/similar` | 个性化推荐 / 相似书推荐 |
| 阅读概况 | 组合接口 | 书架 + 进度综合展示 |

## 2. 接口与鉴权

```text
POST https://i.weread.qq.com/api/agent/gateway
Content-Type: application/json
Authorization: Bearer $WEREAD_API_KEY   # 已配置，格式 wrk-xxxxxxxx
```

Key 未设置时告知用户：`export WEREAD_API_KEY=<你的apikey>`。

## 3. 请求格式（通用）

- 业务参数**平铺在 body 顶层**，不得包在 `params` / `data` 里；
- 每次请求必须带 `skill_version: "1.0.3"`；
- 回包出现 `upgrade_info` 时按指引升级后再继续；
- 回包 `errcode ≠ 0` 时按中文提示处理。

```bash
curl -X POST "https://i.weread.qq.com/api/agent/gateway" \
  -H "Authorization: Bearer $WEREAD_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"api_name": "/store/search", "keyword": "窄门", "scope": 10, "count": 5, "skill_version": "1.0.3"}'
```

## 4. 能力详解与用法示例

### 4.1 搜索 `/store/search`

**参数**：`keyword`（必填）、`scope`（搜索类型，默认 10 电子书）、`maxIdx`（翻页偏移）、`count`（每页数量）。

**scope 取值**：0=全部、10=电子书、16=网文小说、14=听书/专辑、6=作者、12=全文、13=书单、2=公众号、4=文章。

**scope 选择**：明确"找书/搜书"→ 10；泛搜索"搜一下 xx"→ 0；"网文"→ 16；"听书/有声书"→ 14；"查作者"→ 6。

**用法示例**（书库用 scope=10 只搜电子书）：

```bash
curl -X POST "https://i.weread.qq.com/api/agent/gateway" \
  -H "Authorization: Bearer $WEREAD_API_KEY" -H "Content-Type: application/json" \
  -d '{"api_name": "/store/search", "keyword": "西线无战事", "scope": 10, "count": 5, "skill_version": "1.0.3"}'
```

**回包关键字段**：`results[].books[].bookInfo.{bookId, title, author, translator, cover, intro, publisher, category, newRating, newRatingCount, newRatingDetail, readingCount, soldout}`。

**注意**：`newRating` 是**千分制**（文档写"0-100"有误，实测 739 → App 显示 7.4，需 ÷100 得 10 分制）。

### 4.2 书籍信息 `/book/info`（+ 章节目录 + 阅读进度）

**参数**：`bookId`（必填）。

**用法示例**：

```bash
curl -X POST "https://i.weread.qq.com/api/agent/gateway" \
  -H "Authorization: Bearer $WEREAD_API_KEY" -H "Content-Type: application/json" \
  -d '{"api_name": "/book/info", "bookId": "23736455", "skill_version": "1.0.3"}'
```

**回包关键字段**：`bookId, title, author, translator, cover, intro, category, publisher, publishTime, isbn, wordCount, newRating, newRatingCount`。

> **书库价值**：`isbn` / `publisher` / `intro` 可与豆瓣、本地库做交叉验证（ISBN 不符时留空进报告）。

**章节目录** `/book/chapterinfo`：参数 `bookId`，回包 `chapters[]`（chapterUid/chapterIdx/title/wordCount/level/paid）。章节 UID 供 underlines / bestbookmarks 使用。

**阅读进度** `/book/getprogress`：参数 `bookId`，回包 `book.progress`（**0-100 整数，1 表示 1%，只有 100 表示读完**）、`book.recordReadingTime`（秒，展示转"X小时Y分钟"）。

### 4.3 书架 `/shelf/sync`

**参数**：无（用户身份由 API Key 自动识别）。

**用法示例**：

```bash
curl -X POST "https://i.weread.qq.com/api/agent/gateway" \
  -H "Authorization: Bearer $WEREAD_API_KEY" -H "Content-Type: application/json" \
  -d '{"api_name": "/shelf/sync", "skill_version": "1.0.3"}'
```

**回包**：`books[]`（电子书/导入书）、`albums[]`（有声书/专辑）。书架总数 = `books.length + albums.length + (mp 非空 ? 1 : 0)`。

### 4.4 阅读统计 `/readdata/detail`

**参数**：`mode`（weekly=本周/monthly=本月/annually=本年/overall=总计，默认 monthly）、`baseTime`（0=当前周期；传历史时间戳查该周期）。

**用法示例**：

```bash
curl -X POST "https://i.weread.qq.com/api/agent/gateway" \
  -H "Authorization: Bearer $WEREAD_API_KEY" -H "Content-Type: application/json" \
  -d '{"api_name": "/readdata/detail", "mode": "monthly", "baseTime": 0, "skill_version": "1.0.3"}'
```

**注意**：阅读时长字段单位以 readdata.md 说明为准，禁止凭字段名推断。

### 4.5 笔记与划线

| 接口 | 参数 | 用途 |
|---|---|---|
| `/user/notebooks` | `count`、`lastSort` | 所有有笔记的书概览 |
| `/book/bookmarklist` | `bookId` | 单本书划线内容（自动过滤书签）|
| `/review/list/mine` | `bookid`、`synckey`、`count` | 个人想法/点评/书评 |
| `/book/underlines` | `bookId`、`chapterUid` | 章节划线热度（"X人划线"，不含文本）|
| `/book/bestbookmarks` | `bookId`、`chapterUid` | 章节热门划线内容 |

**用法示例**（导出某本书的划线）：

```bash
curl -X POST "https://i.weread.qq.com/api/agent/gateway" \
  -H "Authorization: Bearer $WEREAD_API_KEY" -H "Content-Type: application/json" \
  -d '{"api_name": "/book/bookmarklist", "bookId": "23736455", "skill_version": "1.0.3"}'
```

### 4.6 书籍点评 `/review/list`

**参数**：`bookId`（必填）、`reviewListType`（0=全部/1=推荐/2=不行/3=最新/4=一般）、`count`、`maxIdx`、`synckey`。

**用法示例**：

```bash
curl -X POST "https://i.weread.qq.com/api/agent/gateway" \
  -H "Authorization: Bearer $WEREAD_API_KEY" -H "Content-Type: application/json" \
  -d '{"api_name": "/review/list", "bookId": "23736455", "reviewListType": 1, "count": 10, "skill_version": "1.0.3"}'
```

### 4.7 推荐好书

| 接口 | 参数 | 用途 |
|---|---|---|
| `/book/recommend` | `count`、`maxIdx` | 个性化推荐（"为你推荐"）|
| `/book/similar` | `bookId`、`count`、`maxIdx`、`sessionId` | 相似书推荐 |

**用法示例**（找《窄门》的相似书）：

```bash
curl -X POST "https://i.weread.qq.com/api/agent/gateway" \
  -H "Authorization: Bearer $WEREAD_API_KEY" -H "Content-Type: application/json" \
  -d '{"api_name": "/book/similar", "bookId": "23736455", "count": 5, "skill_version": "1.0.3"}'
```

### 4.8 阅读概况（组合）

综合 `/shelf/sync` + `/book/getprogress` 展示阅读概况；阅读偏好分析走 `/readdata/detail`。

## 5. 书库评分计算（rating 字段）

```text
rating = round(微信读书分 × 0.7 + 豆瓣分 × 0.3, 1)   # 10 分制，保留一位小数
微信读书分 = newRating / 100                        # 千分制换算（739 → 7.4）
豆瓣分     = 豆瓣详情页 property="v:average"
```

缺失处理：

- 微信读书无评分 → 只用豆瓣分；
- 豆瓣无评分 → 只用微信读书分；
- 两侧都无 → **不写 `rating`**，写入报告说明。

示例：微信 7.4（739/100）× 0.7 + 豆瓣 8.1 × 0.3 = 5.18 + 2.43 = 7.61 → 写入 `7.6`。

## 6. 版本匹配规则

搜索常返回多个版本，**必须选与库内书一致的版本**：

1. 书名 + 译者（或作者）与库内书匹配的版本优先（如库内是果麦版，选"窄门（果麦经典）"）；
2. 无匹配版本时取第一条结果。

## 7. 异常处理

| 现象 | 处理 |
|---|---|
| 回包 `errcode ≠ 0` | 按回包中文提示处理；仍失败 → 不写评分，报告说明 |
| 请求超时/网络失败 | 间隔 2-3s 重试 3 次；仍失败 → 不写评分，报告说明 |
| `upgrade_info` 出现 | 按指引升级（更新 `skill_version`）后重新执行 |
| Key 未配置 | 告知用户配置 `WEREAD_API_KEY`，暂缓评分 |

## 8. 写入方式

```text
calibredb set_metadata --with-library <库> <id> --field=rating:7.6
```

写完 `set_metadata` 后必须 `embed_metadata` 嵌入文件（遵守《calibre书库管理.md》双写原则）。
