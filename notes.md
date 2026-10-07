# 笔记

对外类型只使用三个词：

- **划线**（`noteType=highlight`）：主要是原文
- **想法**（`noteType=thought`）：含章评
- **书评**（`noteType=book`）：仅微信读书全书点评

## `/notes/list`

| 参数 | 说明 |
|------|------|
| `source` | `weread` / `paper` / `all` |
| `bookId` | 可选 |
| `noteType` | 可选：`highlight` / `thought` / `book` |
| `favorite` | `true` 时只返回收藏 |
| `q` | 可选，正文/想法模糊匹配 |
| `limit` | 1–100，默认 20 |
| `cursor` | 上一页游标 |

### 回包 `items[]`

- `source`、`id`、`bookId`、`bookTitle`、`noteType`
- `markText`：原文/划线
- `reviewText`：想法
- `isFavorite`、`isBlocked`、`createTime`
- 纸质书可能有 `pageNo`

导出时按书分组，优先展示 `markText`，有想法再附 `reviewText`。

## `/notes/get`

| 参数 | 说明 |
|------|------|
| `source` | 必填：`weread` 或 `paper` |
| `id` | 必填：列表项的 `id` |
