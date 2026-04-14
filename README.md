# BSC-amazon-SIF-Tool

用 SIF MCP 分析亚马逊 ASIN 流量、广告、关键词、销量，并把结果进一步整理成可执行的运营结论。

输入 ASIN 或关键词后，可以快速得到：
- 流量趋势与异常判断
- 广告结构与 Campaign 贡献变化
- 关键词健康度、依赖类型、竞争格局
- 可直接落地到飞书多维表格 / 看板的分析结果

---

## 这是什么

这是一个面向 Claude Code / Claude Desktop 的 SIF MCP skill。

它适合以下场景：
- 诊断某个 ASIN 最近为什么流量下跌
- 判断一个关键词现在值不值得做
- 分析广告 Campaign 哪些在承接、哪些在衰减
- 查看关键词对广告的依赖程度
- 输出更量化的运营分析，而不是泛泛建议
- 把分析结果整理成飞书多维表格、动作清单、看板

---

## 能做什么

### 流量诊断
- `analyze_traffic_anomaly`
- `ops_get_asin_traffic_trend`
- `ops_get_listing_traffic_overview`
- `ops_get_listing_traffic_structure`

### 广告分析
- `ads_get_asin_ad_structure`
- `ads_get_asin_ad_traffic_trend`
- `ads_get_asin_campaign_contribution_overview`
- `ads_get_campaign_contribution_breakdown`
- `ads_get_ad_group_traffic_trend`
- `ads_get_ad_group_keyword_breakdown`

### 关键词分析
- `market_get_asin_keyword_signals`
- `market_get_keyword_demand`
- `market_get_keyword_history`
- `market_get_keyword_root_trend`
- `market_get_keyword_competition`

### 销量分析
- `ops_get_asin_sales_trend`
- `ops_get_asin_sales_list`

如果你只是想知道“有哪些工具可用”，可以调用：
- `sif_catalog`
- `ping`

---

## 快速开始

### 1. 获取 SIF Token

前往 `https://www.sif.com` 获取你的 SIF Token。

> Token 具备访问权限，请妥善保管，不要提交到代码仓库。

### 2. 在 Claude Code 中接入 MCP

```bash
claude mcp add --transport http sif-mcp https://mcp.sif.com/mcp \
  --header "Authorization: Bearer 你的SIF_Token"
```

### 3. 验证是否接入成功

```bash
claude mcp list
```

看到类似下面结果即可：

```bash
sif-mcp: https://mcp.sif.com/mcp (HTTP) - Connected
```

---

## Claude Desktop 接入

如果你用的是 Claude Desktop：

1. 打开 `Settings`
2. 进入 `Connectors`
3. 选择 `Add custom connector`
4. 填写：
   - Name: `SIF MCP`
   - URL: `https://mcp.sif.com/mcp`
5. 点击 Connect
6. 输入你的 SIF Token 完成认证

---

## 第一次使用怎么提问

### 示例 1：诊断 ASIN 流量下跌

```text
帮我用 analyze_traffic_anomaly 诊断 B0XXXXXX 最近的流量下跌原因
```

注意：`analyze_traffic_anomaly` 必须写在问题里，不能只说“帮我看看为什么流量跌了”。

### 示例 2：查看流量趋势

```text
帮我查 B0XXXXXX 最近 12 周的流量趋势，重点看总流量、广告流量、自然流量变化
```

### 示例 3：继续深挖广告承接

```text
继续深挖这个 ASIN 的 Campaign 承接变化，找出最近增长和衰减最明显的 Campaign
```

### 示例 4：做关键词判断

```text
帮我判断关键词 pill dispenser 现在是不是适合继续投入，顺便看竞争格局
```

### 示例 5：生成飞书多维表格

```text
把 B0XXXXXX 的诊断结果整理成飞书多维表格，包含总览、Campaign监控、动作清单，并补按优先级和按状态分组视图
```

---

## 推荐使用路径

### 路径 1：ASIN 流量下跌诊断

适合你已经发现流量异常，想快速定位问题在哪。

```text
1. analyze_traffic_anomaly
2. ads_get_asin_ad_traffic_trend
3. ads_get_asin_campaign_contribution_overview
4. ads_get_campaign_contribution_breakdown
5. ads_get_ad_group_keyword_breakdown
6. market_get_asin_keyword_signals
```

说明：
- 先用 `analyze_traffic_anomaly` 拿总判断
- 如果是广告侧问题，再下钻到 Campaign / Ad Group
- 如果怀疑自然侧拖累，再补关键词信号和竞争格局

### 路径 2：关键词研究

适合你想判断一个词值不值得做。

```text
1. market_get_keyword_demand
2. market_get_keyword_history
3. market_get_keyword_root_trend
4. market_get_keyword_competition
```

这四个工具的分工：
- `market_get_keyword_demand`：判断层
- `market_get_keyword_history`：量化层
- `market_get_keyword_root_trend`：边界层
- `market_get_keyword_competition`：竞争层

### 路径 3：广告结构梳理

适合你要看一个 ASIN 现在靠哪些广告类型和 Campaign 在跑。

```text
1. ads_get_asin_ad_structure
2. ads_get_asin_ad_historical_feature_profile
3. ads_get_asin_campaign_contribution_overview
4. ads_get_campaign_structure
5. ads_get_campaign_contribution_breakdown
```

---

## 输出应该长什么样

推荐把结果整理成下面这个结构：

```text
指标 → 当前值/趋势 → 异常解释 → 执行动作
```

例如：
- 总流量：最近 7 天 vs 前 7 天下降 40% → 核心异常
- 广告流量：较上周下降 51% → 主要问题在广告承接
- 自然流量：较上周增长 706% → 自然侧不是全面崩盘
- 核心结论：广告主力 Campaign 切换后承接不足
- 动作建议：先稳住当前承接 Campaign，再复盘历史爆量结构

如果用户要求“更量化”，至少应覆盖：
- 销量趋势
- 总流量 / 广告流量 / 自然流量
- 流量结构占比
- Campaign 贡献变化
- 关键词流量贡献、排名、依赖类型、健康度

---

## 数据边界说明

当前这套 SIF 工具文档里，没有直接提供以下广告后台绩效字段：
- 广告花费（Spend）
- CTR
- CPC
- CVR
- ACOS
- ROAS
- 广告订单 / 广告销售额
- 严格口径的订单转化率

所以：
- 不能编造这些指标
- 不能把流量数据假装成转化数据
- 如果用户要这些指标，必须明确说明需要额外提供 Amazon Ads 报表或对应数据源

目前可直接量化的主要是：
- 销量趋势
- 总流量 / 自然流量 / 广告流量
- Listing 渠道占比
- Campaign / 广告组 / 关键词贡献变化
- 关键词自然位、广告位、流量贡献、依赖类型、健康度

---

## 如何把结果落地成飞书多维表格

当用户明确说要“生成飞书表格”“做看板”“补动作清单”时，推荐直接落地，而不是只给空表建议。

### 最小可交付表结构

#### 1. 总览表
建议字段：
- 日期区间
- ASIN
- 结论
- 近7天变化
- 广告变化
- 自然变化
- 核心问题
- 优先级

#### 2. Campaign监控表
建议字段：
- Campaign ID
- 显示ID
- 类型
- 创建日期
- 分析周期
- 贡献分
- 占比
- 阶段角色
- 结论
- 建议动作

#### 3. 动作清单表
建议字段：
- 动作名称
- 负责人
- 优先级
- 状态
- 开始日期
- 截止日期
- 预期影响
- 备注

### 推荐增强表

如果用户要“更细”“更适合运营诊断”，建议再加：
- 流量趋势表
- 流量结构表
- 关键词信号表
- Campaign变更表
- 广告结构画像表

### 推荐视图

至少补这两个：
- `按优先级分组`
- `按状态分组`

如果是关键词和诊断表，还建议补：
- `按关键词健康度分组`
- `按流量依赖分组`

### 推荐 Dashboard 模块

建议优先放：
- 总流量 / 自然 / 广告趋势
- 自然占比趋势
- 流量结构对比
- 关键词健康度分布
- 关键词依赖类型分布
- Top 关键词流量占比
- 结论优先级分布
- 动作执行状态分布

---

## 实战示例

下面是一类典型的诊断结论写法：

```text
ASIN = B0GHMT7NS4
最近 30 天流量高波动
近 7 天 vs 前 7 天总流量 -40%
系统判断：ad_issue
广告流量较上周 -51%
自然流量较上周 +706%
```

这类结果更适合这样继续落地：
- 在总览表里写“广告承接断层，不是自然全面崩盘”
- 在 Campaign 监控表里写清楚：
  - 哪个是历史爆量主力
  - 哪个是当前承接主力
  - 哪些在衰减
- 在动作清单表里拆成：
  - 稳定当前承接 Campaign
  - 复用历史爆量结构
  - 修复高风险关键词承接
  - 评估旧 Campaign 是否保留

---

## 常见问题

### `analyze_traffic_anomaly` 为什么没有触发？

因为这个工具不会根据语义自动路由。你必须在问题里明确写出工具名：`analyze_traffic_anomaly`。

### `market_get_keyword_demand` 只支持美国站吗？

季节性识别和节假日预警目前仅支持 `US`。
其他市场仍可看生命周期趋势，但季节性数据不可用。

### `ads_get_campaign_contribution_breakdown` 为什么报日期错误？

`start_date` 和 `end_date` 必须精确构成一个自然周，也就是：
- `end_date = start_date + 6 天`

### 广告组关键词级别为什么有时拿不到？

如果 `ads_get_ad_group_keyword_breakdown` 没返回有效数据，不要硬推结论。
这时可以先停在 Campaign / Ad Group 层，并明确告诉用户当前下钻边界。

### Claude Code 里看不到 SIF 工具？

因为 Claude 网页端的 MCP 配置和 Claude Code CLI 的 MCP 配置不是同一套。
请在终端重新执行：

```bash
claude mcp add --transport http sif-mcp https://mcp.sif.com/mcp \
  --header "Authorization: Bearer 你的SIF_Token"
```

---

## 文件结构

```text
BSC-amazon-SIF-Tool/
├── README.md
├── SKILL.md
└── references/
    └── sif-tools-quick-ref.md
```

- `README.md`：新手安装与使用说明
- `SKILL.md`：完整技能定义、调用规则、输出要求、飞书落地方法
- `references/sif-tools-quick-ref.md`：工具参数速查表

---

## 适合谁用

这个 skill 特别适合：
- 亚马逊运营
- 广告投放分析
- 关键词研究
- 用 Claude 做数据诊断和运营跟进的人
- 希望把分析结果直接落地到飞书协作的人

如果你已经有具体 ASIN 或关键词，可以直接开始提问。