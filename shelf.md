# 书架

## `/shelf/list`

| 参数 | 说明 |
|------|------|
| `source` | `weread` / `paper` / `all`（默认） |
| `limit` | 1–100，默认 20 |
| `cursor` | 上一页 `paging.nextCursor` |
| `q` | 可选，书名/作者/出版社模糊匹配 |

### 回包 `items[]`

共用字段：`source`、`bookId`、`title`、`author`、`publisher`、`updatedAt`

- 微信读书另有：`cover`、`highlightCount`、`reviewCount`、`finishReading`、`isBlocked`
- 纸质书另有：`coverFileId`、`isUnnamed`、`createdAt`

计数口径：回答「书架有多少本」优先调 `/library/summary` 的 `total.bookCount`，不要只数当前页。
