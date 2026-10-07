---
name: huaxianji
description: 划线集助手 — 只读查阅用户在划线集中的微信读书副本与纸质书笔记、书架、阅读统计。在用户提到划线集、书摘、纸质书笔记、导出划线、阅读足迹，或设置了 HUAXIANJI_API_KEY 时使用。
version: 1.0.0
---

# 划线集 — 阅读笔记助手

通过 Agent API Gateway 只读访问用户在「划线集」小程序中的数据（微信读书同步副本 + 纸质书笔记）。

## 支持的能力

| 能力 | 说明 | 用户示例 | 详细说明 |
|------|------|----------|----------|
| 书库摘要 | 书本数、笔记数、上次同步 | "我在划线集存了多少笔记" | `library.md` |
| 书架 | 微信读书书 + 纸质书 | "看看我的书架" | `shelf.md` |
| 笔记列表 | 划线 / 想法 / 书评 | "导出三体里的划线" | `notes.md` |
| 单条笔记 | 按 id 取详情 | "把这条笔记全文给我" | `notes.md` |
| 阅读统计 | 足迹摘要、收藏数 | "我的阅读足迹怎么样" | `stats.md` |

根据用户意图先读对应说明文件，再调接口。

---

## 接口调用规范

### 统一入口

```
POST https://cloud1-d2gile4rp6245c1e1-1386517037.ap-shanghai.app.tcloudbase.com/agent
```

（路径等价：`/agent`、`/agent/gateway`、`/` 均可，推荐上面这一条。）

### 鉴权

- Header：`Authorization: Bearer $HUAXIANJI_API_KEY`
- `HUAXIANJI_API_KEY` 从环境变量获取，格式 `hxk_xxxxxxxx`
- 若未设置，提示用户：到划线集小程序「我的 → AI Skill 接入」生成 Key，然后 `export HUAXIANJI_API_KEY=<你的apikey>`
- API Key 绑定微信用户；所有个人数据接口自动限定到该用户，无需传 openid

### 请求格式

- **Method**：POST
- **Content-Type**：application/json
- **Body**：JSON，`api_name` 指定接口，其余为接口参数，**每次请求必须带 `skill_version`**

```bash
curl -X POST "https://cloud1-d2gile4rp6245c1e1-1386517037.ap-shanghai.app.tcloudbase.com/agent" \
  -H "Authorization: Bearer $HUAXIANJI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"api_name":"/library/summary","skill_version":"1.0.0"}'
```

### 请求 few-shot

**正确：业务参数平铺在 body 顶层。**

```json
{"api_name":"/notes/list","source":"all","limit":20,"skill_version":"1.0.0"}
```

**正确：下一页继续平铺 `cursor`。**

```json
{"api_name":"/notes/list","source":"weread","limit":20,"cursor":"<上一页 paging.nextCursor>","skill_version":"1.0.0"}
```

**错误：不要把业务参数包在 `params` 内。**

```json
{"api_name":"/notes/list","params":{"limit":20},"skill_version":"1.0.0"}
```

### 响应格式

- JSON；`errcode === 0` 且 `code === "OK"` 表示成功
- 失败时 `errcode` 非 0，并带中文 `message`
- 发送 `{"api_name":"/_list","skill_version":"1.0.0"}` 可查看全部接口

### 通用规则

1. **版本上报**：每次请求 body 必须包含 `"skill_version": "1.0.0"`（取本文件顶部 version）。若回包出现 `upgrade_info`，必须先按其中指引升级 Skill，再重试用户请求。
2. **参数平铺**：业务参数与 `api_name`、`skill_version` 同层，不要包在 `params` / `data` 里。
3. **能力文档预检**：调用前先读对应说明文件（书架读 `shelf.md`，笔记读 `notes.md`）。
4. **来源字段**：`source` 为 `weread`（微信读书副本）或 `paper`（纸质书）；列表默认 `all`。
5. **书名解析**：用户只说书名时，先 `/shelf/list` 带 `q` 搜书拿到 `bookId`，再查笔记。
6. **分页**：用回包 `paging.nextCursor` 原样传入下一页的 `cursor`；`hasMore === false` 停止。
7. **只读**：本 Skill **禁止**尝试写入、删除、同步微信读书或修改会员状态。
8. **隐私**：不要把完整 API Key 写进聊天记录或提交到 git；展示列表时用书名与摘录即可。
9. **时间展示**：Unix 毫秒时间戳展示为 `YYYY-MM-DD`。
10. **书名展示**：界面与回复里的书名不加书名号《》。

---

## 健康检查

```bash
curl "https://cloud1-d2gile4rp6245c1e1-1386517037.ap-shanghai.app.tcloudbase.com/agent/health"
```
