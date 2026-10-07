# 划线集 Agent Skill

让第三方 AI（Cursor / Claude 等）只读访问你在划线集中的书架与笔记。

## 安装

```bash
npx skills add helicopters/huaxianji-skills -g
```

## 配置

1. 打开划线集小程序 → **我的 → AI Skill 接入**
2. 生成并复制 API Key（`hxk_` 开头）
3. 设置环境变量：

```bash
export HUAXIANJI_API_KEY=hxk_xxxxxxxx
```

## 试用

```
看看我在划线集存了多少笔记
列出最近的收藏划线
三体里有哪些想法
```

## 网关

```
POST https://cloud1-d2gile4rp6245c1e1-1386517037.ap-shanghai.app.tcloudbase.com/agent
Authorization: Bearer $HUAXIANJI_API_KEY
```

详见 `SKILL.md`。
