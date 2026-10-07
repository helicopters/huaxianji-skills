# 阅读统计

## `/stats/overview`

无业务参数。在 `/library/summary` 基础上增加：

- `favorites`：收藏总数（微信读书 + 纸质书）
- `libraryStats`：物化书架统计（可能为 null）
- `noteDayStats`：活跃天数、最早一天、划线最多的一天（可能为 null）

若物化字段为空，仍可用 `total` / `favorites` 回答概览，并说明详细热力需打开小程序统计页。
