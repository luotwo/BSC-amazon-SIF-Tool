# SIF MCP 工具快速参考

> 本文件是参数速查手册，配合 `SKILL.md` 使用。
> 数据来源：https://mcp.sif.com/mcp/catalog（2026-04-14 快照，共 27 个工具）

---

## 选工具前先问自己三个问题

| 我想做什么 | 推荐起点工具 |
|-----------|-------------|
| 诊断某 ASIN 流量为什么跌 | `analyze_traffic_anomaly` |
| 看 ASIN 广告总体结构和历史 | `ads_get_asin_ad_structure` + `ads_get_asin_ad_historical_feature_profile` |
| 找流量最大的 Campaign | `ads_get_asin_campaign_contribution_overview` |
| 判断某个关键词值不值得做 | `market_get_keyword_demand` |
| 看 ASIN 整体流量趋势 | `ops_get_asin_traffic_trend` |
| 确认 SIF 工具连通是否正常 | `ping` |

---

## 一、系统工具

### `ping`

**干什么**：检查 SIF MCP 连通性。

**参数**：无。

**什么时候用**：第一次接入时，或工具没反应时先 ping 一下。

---

### `sif_catalog`

**干什么**：返回所有 27 个工具的分类列表。

**参数**：无。

**什么时候用**：不确定用哪个工具时，用这个先看目录。

---

## 二、流量诊断工具

### `analyze_traffic_anomaly` ⭐

**干什么**：自动判断流量下跌是广告侧问题还是自然侧问题，给出方向性结论。

**参数**：

| 参数 | 必填 | 说明 |
|------|------|------|
| `asin` | 是 | 目标 ASIN |
| `marketplace` | 否 | 站点代码，默认 US |
| `time_type` | 否 | all / week / month |
| `time_value` | 否 | time_type=week/month 时必填 |

**示例提问**：
```
帮我用 analyze_traffic_anomaly 诊断 B0GHMT7NS4 最近的流量下跌
```

**注意**：
- 必须在提问中显式写出 `analyze_traffic_anomaly`，否则不会自动触发。
- 输出是方向性判断（如 `ad_issue`），不能替代 Campaign 层的量化下钻。

---

### `ops_get_asin_traffic_trend`

**干什么**：查看 ASIN 流量的时间序列，拆分为 SP / SB / SBV / 自然等渠道。

**参数**：

| 参数 | 必填 | 说明 |
|------|------|------|
| `asin` | 是 | 目标 ASIN |
| `granularity` | 是 | week 或 month |
| `marketplace` | 否 | 默认 US |
| `lastMonths` | 否 | 最近 N 个月窗口 |

**什么时候用**：
- 确认流量变化时间窗口。
- 判断广告流量和自然流量是否同步变化。

---

### `ops_get_asin_traffic_trend_detail`

**干什么**：拉取某个时间窗口内关键词级别的流量明细。

**参数**：

| 参数 | 必填 | 说明 |
|------|------|------|
| `asin` | 是 | 目标 ASIN |
| `endDay` | 是 | 窗口结束日期（yyyy-MM-dd） |
| `granularity` | 是 | 建议用 day |
| `listingSearch` | 是 | 是否用 listingSearch 口径 |
| `pageNum` / `pageSize` | 是 | 分页，pageSize 建议 ≤ 200 |
| `keywordType` | 否 | all / nf / ad / sp / sb / sbv |

**注意**：用于取原始关键词明细；根因分析用 `analyze_traffic_anomaly`，不要混用。

---

### `ops_get_listing_traffic_overview`

**干什么**：查看 Listing 整体流量全景，按渠道（自然 / SP / SB / SBV）给出占比。

**参数**：

| 参数 | 必填 | 说明 |
|------|------|------|
| `asin` | 是 | 任意变体 ASIN |
| `marketplace` | 否 | 默认 US |
| `timePieceType` | 否 | week 或 month |
| `timePieceValue` | 否 | 格式 yyyy-Www / yyyy-MM |

**什么时候用**：需要看流量渠道结构比例时，先调这个。

---

### `ops_get_listing_traffic_structure`

**干什么**：各变体在不同渠道的流量分数与占比。

**参数**：

| 参数 | 必填 | 说明 |
|------|------|------|
| `asin` | 是 | 目标 ASIN |
| `marketplace` | 否 | 默认 US |
| `timePieceType` | 否 | week 或 month |

**什么时候用**：做多变体对比、确认哪个变体承载了主要流量时。

---

## 三、广告分析工具

### ASIN 层（先调这里）

#### `ads_get_asin_ad_structure`

**干什么**：统计该 ASIN 在 SP / SB / SBV 下分别有多少个 Campaign。

**参数**：

| 参数 | 必填 | 说明 |
|------|------|------|
| `asin` | 是 | 目标 ASIN |
| `marketplace` | 是 | 站点代码 |

**什么时候用**：广告分析的第一步，了解整体规模，决定后续方向。

---

#### `ads_get_asin_ad_traffic_trend`

**干什么**：SP / SB / SBV 三渠道广告曝光量历史时序。

**参数**：

| 参数 | 必填 | 说明 |
|------|------|------|
| `asin` | 是 | 目标 ASIN |
| `marketplace` | 是 | 站点代码 |
| `granularity` | 否 | week 或 month |

**什么时候用**：定位"哪种广告类型、哪个时间段出现了变化"。

---

#### `ads_get_asin_ad_historical_feature_profile`

**干什么**：长期历史广告特征画像，判断打法演变、成熟度、主导广告类型。

**参数**：

| 参数 | 必填 | 说明 |
|------|------|------|
| `asin` | 是 | 目标 ASIN |
| `marketplace` | 是 | 站点代码 |
| `granularity` | 否 | week（默认）或 month |

**什么时候用**：广告深度分析的必读背景。在调 Campaign 详情前先跑一次，建立对广告体系演化的理解。

---

#### `ads_get_asin_ad_window_feature_profile`

**干什么**：指定时间窗口的广告特征画像（集中度、投放节奏、渠道信号）。

**参数**：

| 参数 | 必填 | 说明 |
|------|------|------|
| `asin` | 是 | 目标 ASIN |
| `marketplace` | 是 | 站点代码 |
| `start_date` | 是 | yyyy-MM-dd |
| `end_date` | 是 | yyyy-MM-dd |
| `ad_type` | 否 | SP / SB / SBV / SB_SBV |

**注意**：`ads_get_asin_ad_feature_profile` 是它的别名，参数相同，新场景用这个。

---

#### `ads_get_asin_campaign_contribution_overview`

**干什么**：按贡献排名列出所有 Campaign，返回每个 Campaign 的曝光分、占比、排名。

**参数**：

| 参数 | 必填 | 说明 |
|------|------|------|
| `asin` | 是 | 目标 ASIN |
| `marketplace` | 是 | 站点代码 |
| `start_date` | 是 | 分析窗口开始日期 |
| `end_date` | 是 | 分析窗口结束日期 |
| `ad_type` | 否 | 按类型过滤 |
| `limit` | 否 | 返回条数，默认 20，最大 200 |

**什么时候用**：定位"哪个 Campaign 贡献骤变"。这是广告下钻的必经入口。

---

#### `ads_get_asin_campaign_changes`

**干什么**：返回该 ASIN 历史上 Campaign 新建的时间节点事件。

**参数**：

| 参数 | 必填 | 说明 |
|------|------|------|
| `asin` | 是 | 目标 ASIN |
| `country` | 是 | 站点代码（此工具用 country 参数） |

**什么时候用**：确认"流量变化时间点附近有没有 Campaign 结构变化"。

---

### Campaign 层（定位后下钻）

#### `ads_get_campaign_structure`

**干什么**：查看某个 Campaign 下所有广告组的历史详情（变体数、关键词数、创建日期）。

**参数**：

| 参数 | 必填 | 说明 |
|------|------|------|
| `asin` | 是 | 目标 ASIN |
| `campaignId` | 是 | 支持短 ID（显示 ID）或加密 ID |

---

#### `ads_get_campaign_traffic_trend`

**干什么**：Campaign 全生命周期流量趋势，附带广告组创建事件标记。

**参数**：

| 参数 | 必填 | 说明 |
|------|------|------|
| `asin` | 是 | 目标 ASIN |
| `campaignId` | 是 | 支持短 ID 或加密 ID |

**什么时候用**：判断"这个 Campaign 是在增长、稳定还是衰减"。

---

#### `ads_get_campaign_contribution_breakdown`

**干什么**：Campaign 单周内按关键词或广告组维度的贡献拆分，含流量变化率。

**参数**：

| 参数 | 必填 | 说明 |
|------|------|------|
| `asin` | 是 | 目标 ASIN |
| `campaignId` | 是 | 支持短 ID 或加密 ID |
| `start_date` | 是 | 自然周起始日期（yyyy-MM-dd） |
| `end_date` | 是 | 必须 = start_date + 6 天 |
| `breakdown_by` | 是 | keyword 或 ad_group |

**常见错误**：`end_date` 不等于 `start_date + 6 天` 会报日期错误，务必精确。

---

### Ad Group 层（进一步下钻）

#### `ads_get_ad_group_traffic_trend`

**干什么**：广告组维度的完整历史流量趋势。

**参数**：

| 参数 | 必填 | 说明 |
|------|------|------|
| `asin` | 是 | 目标 ASIN |
| `campaignId` | 是 | 所属 Campaign ID |
| `adGroupId` | 是 | 广告组 ID（支持短 ID 或加密 ID） |

---

#### `ads_get_ad_group_keyword_breakdown`

**干什么**：广告组单周关键词维度流量贡献，含展示 ASIN 和组内流量占比。

**参数**：

| 参数 | 必填 | 说明 |
|------|------|------|
| `asin` | 是 | 目标 ASIN |
| `campaignId` | 是 | 所属 Campaign ID |
| `adGroupId` | 是 | 广告组 ID |
| `date` | 是 | 周窗口开始日期（yyyy-MM-dd） |

**注意**：如果返回空或报 `adGroupId is not found in campaignAdIds data`，说明该广告组当周数据不可用，不要强行推断，停在上一层汇报边界。

---

## 四、关键词分析工具

### `market_get_keyword_demand` ⭐

**干什么**：判断关键词生命周期阶段、距旺季峰值周数，给出是否值得投入的行动建议。

**参数**：

| 参数 | 必填 | 说明 |
|------|------|------|
| `keywords` | 是 | 1-5 个关键词的数组 |
| `marketplace` | 否 | 默认 US |

**注意**：季节性识别和节假日预警仅支持 US；其他市场可看生命周期趋势，但季节性数据不可用。

**示例提问**：
```
帮我用 market_get_keyword_demand 判断 pill dispenser 现在值不值得继续投
```

---

### `market_get_keyword_history`

**干什么**：返回关键词 ABA 搜索量 / 排名 / Top3 竞品集中度的历史原始数据。

**参数**：

| 参数 | 必填 | 说明 |
|------|------|------|
| `keywords` | 是 | 1-5 个关键词的数组 |
| `marketplace` | 否 | 默认 US |
| `granularity` | 否 | week（默认）或 month |

**什么时候用**：需要量化关键词需求波动时，比 `demand` 更原始。

---

### `market_get_keyword_root_trend`

**干什么**：比较词根综合需求 vs 精确词，判断市场边界与需求分散度。

**参数**：

| 参数 | 必填 | 说明 |
|------|------|------|
| `keyword` | 是 | 单个关键词 |
| `marketplace` | 否 | 默认 US |
| `granularity` | 否 | week 或 month |

**什么时候用**：判断"这个词是需求集中的大词，还是已经分散到长尾词"。

---

### `market_get_keyword_competition`

**干什么**：前 20 ASIN 流量份额、ABA Top3 集中度历史、竞争可进入性评估。

**参数**：

| 参数 | 必填 | 说明 |
|------|------|------|
| `keyword` | 是 | 单个关键词 |
| `marketplace` | 否 | 默认 US |
| `asin` | 否 | 提供后附带该 ASIN 的竞争位置洞察 |
| `time_type` | 否 | all / week / month |

---

### `market_get_asin_keyword_signals`

**干什么**：ASIN 在各关键词上的排名、流量贡献、渠道依赖度、稳定性、健康分级。

**参数**：

| 参数 | 必填 | 说明 |
|------|------|------|
| `asin` | 是 | 目标 ASIN |
| `time_type` | 否 | all / week / month |
| `time_value` | 否 | time_type=week/month 时必填 |

**什么时候用**：发现自然流量异常后，用这个找"哪些关键词在掉排名 / 流量依赖类型有风险"。

---

### `ops_get_listing_keyword_distribution`

**干什么**：各变体在 SP / SB / SBV / 自然渠道中分别覆盖了多少个流量词。

**参数**：

| 参数 | 必填 | 说明 |
|------|------|------|
| `asin` | 是 | 目标 ASIN |
| `marketplace` | 否 | 默认 US |
| `timePieceType` | 否 | week 或 month |

---

## 五、销量工具

### `ops_get_asin_sales_trend`

**干什么**：各变体月度销量历史趋势，支持父 ASIN。

**参数**：

| 参数 | 必填 | 说明 |
|------|------|------|
| `asin` | 是 | 目标 ASIN，支持父 ASIN |
| `country` | 否 | 站点代码，默认 US |

---

### `ops_get_asin_sales_list`

**干什么**：Listing 维度销量 ASIN 排行，含价格、属性、月度迷你图。

**参数**：

| 参数 | 必填 | 说明 |
|------|------|------|
| `asins` | 是 | ASIN 数组，支持多个 |
| `country` | 否 | 站点代码，默认 US |
| `timePieceType` | 否 | week 或 month |

---

## 六、常用诊断路径速查

### 路径 A：流量下跌诊断

```
1. analyze_traffic_anomaly               ← 拿方向判断（ad_issue / organic_issue）
2. ops_get_asin_traffic_trend            ← 确认下跌时间窗口和渠道分布
3. ads_get_asin_campaign_contribution_overview ← 找贡献变化最大的 Campaign
4. ads_get_campaign_traffic_trend        ← 确认具体 Campaign 趋势
5. ads_get_campaign_contribution_breakdown ← 关键词 / 广告组层下钻
6. market_get_asin_keyword_signals       ← 如果怀疑自然侧也有问题
```

### 路径 B：关键词研究

```
1. market_get_keyword_demand             ← 判断层（值不值得做）
2. market_get_keyword_history            ← 量化层（需求有多大）
3. market_get_keyword_root_trend         ← 边界层（词根还是精确词）
4. market_get_keyword_competition        ← 竞争层（进入难度）
```

### 路径 C：广告结构梳理

```
1. ads_get_asin_ad_structure             ← 快速评估规模
2. ads_get_asin_ad_historical_feature_profile ← 了解打法演化背景
3. ads_get_asin_campaign_contribution_overview ← 找当前主力 Campaign
4. ads_get_campaign_structure            ← 看选定 Campaign 的广告组构成
5. ads_get_campaign_contribution_breakdown ← 拆关键词贡献
```

---

## 七、数据边界（不可编造的指标）

SIF MCP **没有**以下广告后台绩效数据：

- 广告花费（Spend）
- CTR / CPC / CVR
- ACOS / ROAS
- 广告订单 / 广告销售额
- 严格口径转化率

如果用户要这些指标，必须明确说明需要从 Amazon Ads 报表另行获取，不能用流量数据假装替代。

**当前可量化的范围**：

- 总流量 / 自然流量 / 广告流量及趋势
- Listing 渠道占比结构
- Campaign / 广告组 / 关键词贡献变化
- 关键词自然位、广告位、流量贡献、依赖类型、健康度
- 销量趋势

---

## 八、常见错误处理

| 错误现象 | 原因 | 解决方法 |
|---------|------|---------|
| `analyze_traffic_anomaly` 没触发 | 没有在提问里写出工具名 | 提问中必须显式写出工具名 |
| `contribution_breakdown` 报日期错误 | end_date 不等于 start_date + 6 天 | 精确补到自然周，例：start=2026-04-07，end=2026-04-13 |
| `ad_group_keyword_breakdown` 返回空 | 该广告组当周数据不可用 | 停在 Campaign / Ad Group 层，明确告知用户数据边界 |
| 季节性判断不可用 | 非 US 市场 | 其他市场仅看生命周期趋势，跳过季节性结论 |
| `market_get_keyword_demand` 只支持 1-5 个词 | API 限制 | 超过 5 个词拆成多批调用 |
