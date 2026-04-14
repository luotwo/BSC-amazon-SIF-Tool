---
name: BSC-amazon-SIF-Tool
description: "当用户需要用 SIF MCP 分析亚马逊 ASIN 流量、销量、关键词、广告架构或诊断流量异常时，必须使用本 skill。覆盖所有 27 个 SIF 工具的调用方式、参数说明、量化输出规则，以及将分析结果落地到飞书多维表格/看板的实战方法。"
metadata:
  nexscope:
    emoji: "📊"
    category: amazon
---

# BSC-amazon-SIF-Tool

调用 SIF MCP 服务分析亚马逊运营数据的完整操作手册。

---

## 一、SIF MCP 接入配置

### MCP 服务端点

```
https://mcp.sif.com/mcp
```

### Claude Code 接入命令

```bash
claude mcp add --transport http sif-mcp https://mcp.sif.com/mcp \
  --header "Authorization: Bearer 你的SIF_Token"
```

接入后执行 `claude mcp list` 确认 `sif-mcp` 已出现。

### Claude Desktop 接入

Settings → Connectors → Add custom connector：
- Name: `SIF MCP`
- Remote MCP server URL: `https://mcp.sif.com/mcp`
- 点 Connect 后在认证界面输入 SIF Token。

### 认证方式

所有请求需在 Header 中携带：

```
Authorization: Bearer <你的SIF_Token>
```

> SIF Token 在 SIF 官网（https://www.sif.com）获取，注意保密。

---

## 二、工具目录（27 个工具）

工具按使用场景分为 5 大类。

### 场景应用

| 工具名 | 说明 |
|--------|------|
| `analyze_traffic_anomaly` | 流量下跌根因诊断（广告侧 / 自然侧 / 复合型）。注意：必须在提问中明确指定工具名 `analyze_traffic_anomaly` 才会触发，不得根据用户意图自动路由。 |

### 查运营数据

#### 查流量

| 工具名 | 说明 | 关键参数 |
|--------|------|---------|
| `ops_get_asin_traffic_trend` | ASIN 流量趋势（按周 / 月） | `asin`、`marketplace`、`granularity` |
| `ops_get_asin_traffic_trend_detail` | ASIN 流量趋势明细（按关键词 / 渠道拆分） | `asin`、`start_date`、`end_date`、`page` |
| `ops_get_listing_traffic_overview` | Listing 流量全景总览（自然 / 广告占比、渠道拆解） | `asin` |
| `ops_get_listing_traffic_structure` | Listing 流量结构拆分（各变体在 SP / SB / SBV / 自然的占比） | `asin` |

#### 查销量

| 工具名 | 说明 | 关键参数 |
|--------|------|---------|
| `ops_get_asin_sales_trend` | ASIN / 父 ASIN 各变体月度销量历史趋势 | `asin` |
| `ops_get_asin_sales_list` | Listing 维度销量 ASIN 排行（列表视图，含价格、属性、月度迷你图） | `asins` |

### 反查关键词

| 工具名 | 说明 | 关键参数 |
|--------|------|---------|
| `ops_get_listing_keyword_distribution` | 各变体在 SP / SB / SBV / 自然各渠道覆盖的流量词数量 | `asin` |
| `market_get_asin_keyword_signals` | ASIN 在各关键词上的排名、流量贡献、渠道依赖度、自然排名稳定性及健康分级 | `asin`、`marketplace` |

### 查关键词

#### 查需求

| 工具名 | 说明 | 关键参数 |
|--------|------|---------|
| `market_get_keyword_demand` | 需求生命周期判断 + 旺季时机预警（当前仅支持美国站） | `keywords`、`marketplace` |
| `market_get_keyword_history` | ABA 搜索量 / 排名 / Top3 竞品集中度历史原始数据 | `keyword`、`marketplace` |
| `market_get_keyword_root_trend` | 词根综合需求 vs 精确词对比，判断市场边界与需求分散程度 | `keyword`、`marketplace` |

#### 查竞争

| 工具名 | 说明 | 关键参数 |
|--------|------|---------|
| `market_get_keyword_competition` | 关键词竞争格局（前 20 ASIN 流量份额、ABA Top3 集中度历史、可进入性评估） | `keyword`、`asin`、`marketplace` |

### 查广告

#### ASIN 层

| 工具名 | 说明 | 关键参数 |
|--------|------|---------|
| `ads_get_asin_ad_structure` | 广告结构总览——各广告类型 Campaign 数量统计 | `asin` |
| `ads_get_asin_ad_traffic_trend` | 广告整体流量趋势（SP / SB / SBV 渠道曝光时序） | `asin`、`marketplace` |
| `ads_get_asin_ad_window_feature_profile` | 指定时间窗口内广告特征画像（集中度、渠道结构、投放节奏） | `asin`、`start_date`、`end_date`、`ad_type` |
| `ads_get_asin_ad_historical_feature_profile` | 长期历史广告特征画像（打法演变、成熟度、渠道策略脉络） | `asin`、`marketplace` |
| `ads_get_asin_ad_feature_profile` | 兼容别名，功能同 `ads_get_asin_ad_window_feature_profile`，新场景优先用上面那个 | `asin`、`start_date`、`end_date`、`ad_type` |
| `ads_get_asin_campaign_contribution_overview` | 各 Campaign 贡献总览（曝光得分排序） | `asin`、`start_date`、`end_date` |
| `ads_get_asin_campaign_changes` | Campaign 历史变更事件（新上线 Campaign 时间节点） | `asin` |

#### Campaign 层

| 工具名 | 说明 | 关键参数 |
|--------|------|---------|
| `ads_get_campaign_structure` | 指定 Campaign 的广告组列表（历史累计变体数、关键词数） | `campaignId` |
| `ads_get_campaign_traffic_trend` | Campaign 全生命周期流量趋势（附广告组创建事件） | `campaignId` |
| `ads_get_campaign_contribution_breakdown` | Campaign 内部贡献拆分（按 keyword 或 ad_group 维度；`start_date` 和 `end_date` 必须精确构成一个自然周） | `campaignId`、`start_date`、`end_date`、`breakdown_by` |

#### Ad Group 层

| 工具名 | 说明 | 关键参数 |
|--------|------|---------|
| `ads_get_ad_group_traffic_trend` | 广告组完整历史流量趋势 | `campaignId`、`adGroupId` |
| `ads_get_ad_group_keyword_breakdown` | 广告组内各关键词流量贡献及绑定 ASIN | `campaignId`、`adGroupId`、`date` |

### 系统工具

| 工具名 | 说明 |
|--------|------|
| `sif_catalog` | 返回所有可用工具分类目录。用户问“有哪些工具”“能做什么”时调用 |
| `ping` | 连通性检查 |

---

## 三、参数规范

| 参数 | 说明 |
|------|------|
| `asin` | 亚马逊 ASIN，支持主 ASIN、父 ASIN 或任意变体 ASIN（工具说明里会注明） |
| `marketplace` | 市场代码，如 `US`、`UK`、`DE`、`JP`，默认 `US` |
| `start_date` / `end_date` | 日期格式 `yyyy-MM-dd` |
| `campaignId` | 支持 `encryptCampaignId` 和 `fakeCampaignId` 两种格式 |
| `adGroupId` | 支持 `fakeAdId` 和 `encryptAdId` 两种格式 |
| `keywords` | 数组，1-5 个关键词 |

---

## 四、典型分析路径

### 路径 1：ASIN 流量下跌诊断

适用：用户说“流量跌了”“帮我诊断流量”“排名下降”。

**注意：必须在调用中明确使用工具名 `analyze_traffic_anomaly`。**

```
1. analyze_traffic_anomaly   ← 总入口，自动拆因
   → 若广告侧有问题：
2. ads_get_asin_ad_traffic_trend   ← 定位异常时间窗口
3. ads_get_asin_campaign_contribution_overview   ← 找贡献变化最大的 Campaign
4. ads_get_campaign_contribution_breakdown   ← 定位具体关键词或广告组
5. ads_get_ad_group_keyword_breakdown   ← 关键词级别确认
   → 若自然侧有问题：
2. ops_get_asin_traffic_trend   ← 自然流量趋势
3. market_get_asin_keyword_signals   ← 反查哪些关键词排名下滑
4. market_get_keyword_competition   ← 确认是否竞品抢位
5. market_get_keyword_demand   ← 排除市场需求整体萎缩
```

### 路径 2：关键词研究与布局决策

适用：用户问“这个词值不值得做”“现在是不是好时机”。

```
1. market_get_keyword_demand        ← 判断层（生命周期、旺季时机）
2. market_get_keyword_history       ← 量化层（搜索量历史原始数据）
3. market_get_keyword_root_trend    ← 边界层（词根市场大小）
4. market_get_keyword_competition   ← 竞争格局（可进入性）
```

### 路径 3：竞品分析

适用：想知道竞品在哪些词上有优势。

```
1. market_get_asin_keyword_signals  ← 竞品 ASIN 的关键词信号
2. market_get_keyword_competition   ← 对应词的竞争格局
3. ops_get_asin_traffic_trend       ← 竞品流量趋势
```

### 路径 4：广告结构梳理

适用：梳理一个 ASIN 完整的广告投放逻辑。

```
1. ads_get_asin_ad_structure                     ← 广告规模概览
2. ads_get_asin_ad_historical_feature_profile    ← 长期打法特征
3. ads_get_asin_campaign_contribution_overview   ← 各 Campaign 贡献排名
4. ads_get_campaign_structure                    ← 选定 Campaign 的广告组构成
5. ads_get_campaign_contribution_breakdown       ← 关键词 / 广告组贡献拆分
```

### 路径 5：从诊断结果落地到运营跟进

适用：用户希望直接生成飞书多维表格、分析看板、动作清单、汇报底稿。

```
1. analyze_traffic_anomaly 或 ops_get_asin_traffic_trend   ← 先拿总判断
2. ops_get_listing_traffic_overview / structure            ← 拆流量结构
3. ads_get_asin_campaign_contribution_overview             ← 明确 Campaign 主次变化
4. market_get_asin_keyword_signals                         ← 明确核心词风险
5. 输出“结论 + 量化指标 + 动作”                           ← 再落地到 Base / Dashboard
```

---

## 五、输出要求

1. 工具返回中若包含 `render_footer` 字段，必须在回复末尾原文输出该字段内容，不得省略。
2. 分析结论先给判断，再给下一步建议。
3. 若用户问的是关键词需求，三个需求工具的分工：
   - `market_get_keyword_history` = 量化层（现在搜索量多少）
   - `market_get_keyword_root_trend` = 边界层（市场盘子多大）
   - `market_get_keyword_demand` = 判断层（现在该怎么行动）
4. 若用户要求“更量化”“不要泛分析”，输出必须优先按以下维度组织，而不是只给方向性结论：
   - 订单/销量：调用 `ops_get_asin_sales_trend` 或 `ops_get_asin_sales_list`
   - 流量结构：调用 `ops_get_asin_traffic_trend`、`ops_get_listing_traffic_overview`、`ops_get_listing_traffic_structure`
   - 广告量化：调用 `ads_get_asin_ad_traffic_trend`、`ads_get_asin_campaign_contribution_overview`、`ads_get_campaign_contribution_breakdown`
   - 关键词转化代理信号：调用 `market_get_asin_keyword_signals`
5. 输出建议尽量使用“指标 → 当前值/趋势 → 异常解释 → 执行动作”的结构。
6. 若用户提到广告消耗金额、ACOS、ROAS、CTR、CPC、CVR、订单转化率，必须先明确数据边界：当前 SIF 文档里没有这些广告后台绩效字段的直接查询工具；不能编造，也不能把流量指标冒充转化指标。
7. 遇到缺少广告后台绩效数据时，必须这样处理：
   - 明确说明当前能直接量化的字段与不能直接量化的字段
   - 先输出基于 SIF 可得数据的量化分析
   - 再补一句：若要继续输出广告消耗、订单归因、CTR、CPC、CVR、ACOS、ROAS，需要用户额外提供 Amazon Ads 报表或对应数据源
8. 用户明确要求“深挖”时，不能停在总览层，必须继续向 Campaign、Ad Group、关键词信号或结构层下钻，直到数据边界阻止继续为止。
9. 若关键词级或广告组级接口没有返回有效数据，不得编造结论；应明确停留在哪一层，以及下一层为什么暂时无法确认。
10. 若用户要求把结果写入飞书多维表格/看板，优先直接执行落地，不要只给一个空 schema 或泛化建议。

---

## 六、飞书多维表格 / 看板落地规则

### 1. 什么时候应该直接落地

满足以下任一条件时，应优先把分析结果落地到飞书：
- 用户明确说“生成飞书多维表格”“做分析看板”“给我动作清单”
- 用户要持续跟踪同一个 ASIN 的诊断、动作、责任人和状态
- 用户不是只看一次性结论，而是要团队协作或后续复盘

### 2. 提问最小化原则

- 如果用户已经给出 Base 链接，优先在现有 Base 上优化，不要擅自再建一个新的。
- 如果用户只说“做一个多维表格”，而没有指定位置，优先直接创建一个新 Base，而不是连续追问可选项。
- 若负责人、截止日期等尚未给出，可以先写 `待补充`、留空或放示例值，先保证结构和核心结论落地。

### 3. 推荐表结构

#### 最小可交付版本

1. `总览表`
   - 日期区间
   - ASIN
   - 结论
   - 近7天变化
   - 广告变化
   - 自然变化
   - 核心问题
   - 优先级

2. `Campaign监控表`
   - Campaign ID
   - 显示ID
   - 类型
   - 创建日期
   - 分析周期
   - 贡献分
   - 占比
   - 阶段角色（历史爆量 / 当前承接 / 衰减）
   - 结论
   - 建议动作

3. `动作清单表`
   - 动作名称
   - 负责人
   - 优先级
   - 状态
   - 开始日期
   - 截止日期
   - 预期影响
   - 备注

#### 推荐增强版本

若用户要求“更细”“更适合运营诊断”，建议补这些表：
- `流量趋势表`：周 / 月总流量、广告流量、自然流量、占比变化
- `流量结构表`：渠道贡献、渠道占比、结构风险
- `关键词信号表`：关键词、流量占比、贡献变化、自然位、广告位、依赖类型、健康度
- `Campaign变更表`：上线日期、变更事件、承接阶段
- `广告结构画像表`：主导广告类型、结构复杂度、投放节奏、阶段特征

### 4. 推荐视图与看板

#### 视图建议

至少补以下视图：
- `按优先级分组`
- `按状态分组`
- `按关键词健康度分组`
- `按流量依赖分组`

若新建视图受限，可复用默认视图并通过重命名 + 分组配置实现。

#### Dashboard 建议

至少优先放这几类块：
- 总流量 / 广告流量 / 自然流量趋势
- 自然占比趋势
- 流量结构对比
- 关键词健康度分布
- 关键词依赖类型分布
- Top 关键词流量占比
- 结论优先级分布
- 动作执行状态分布

### 5. 写入飞书时的内容规则

1. 先写“判断”，再写“依据”，最后写“动作”。
2. 结论必须引用 SIF 的真实指标，不要改写成无法回溯的数据口径。
3. 若当前判断是 `ad_issue`，动作表应优先围绕 Campaign 承接、预算稳定、历史爆量结构复用、关键词承接断层修复来写。
4. 若自然流量并未同步下跌，应明确写出“问题集中在广告承接，而不是自然全面崩盘”。
5. 若用户还没给动作方案，可以先预置空白记录或示例动作，便于后续补充。

### 6. 常见失败处理

- `ads_get_campaign_contribution_breakdown` 日期报错：必须传完整自然周。
- `ads_get_ad_group_keyword_breakdown` 无数据：保留在 Campaign / Ad Group 层，不要硬推关键词结论。
- 飞书新增视图受限：改用默认视图重命名 + 分组。
- 飞书字段接口受限：先建空表，再逐列补字段，再写记录。
- 批量写入中途失败：保留已成功记录，失败部分单独补写，不整表推倒重来。

---

## 七、常见问题

**Q：`analyze_traffic_anomaly` 没有触发？**
该工具需要在用户消息中明确出现工具名本身，不会根据语义自动路由。

**Q：`market_get_keyword_demand` 只支持美国站？**
是的，季节性判断和节假日识别目前仅支持 US marketplace；其他市场可查询生命周期趋势，但季节性数据不可用。

**Q：`ads_get_campaign_contribution_breakdown` 报错 date 格式不对？**
`start_date` 和 `end_date` 必须精确构成一个自然周（`end_date = start_date + 6 天`）。

**Q：Claude Code 里看不到 SIF 工具？**
`claude.ai` 网页端 MCP 与 Claude Code CLI 是两套独立机制。请在终端执行上方 `claude mcp add` 命令单独注册。

**Q：能不能直接输出广告花费、ACOS、ROAS？**
不能直接从当前 SIF 文档支持的工具中得到这些指标。请明确说明数据边界，并提示用户补充 Amazon Ads 报表或对应数据源。
