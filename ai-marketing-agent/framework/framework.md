# AI 营销 Agent 与自动化投放：框架

> 面向对象：有产品背景、正在从 0 到 1 搭建自动化投放体系（Meta 起步、多渠道扩展、远期目标无人干预）的搭建者。
> 底稿：`research-report.md`（2026-11-17 调研报告）。本文是其中文提炼，数字与事实均有出处；**框架结构本身（架构图、阶梯、搭建顺序）是综合推断**，不是任何单一来源的原文。

## 三条核心论点

1. **两层自动化，别搞混。** 平台自动化（Meta Advantage+、Google PMax / AI Max、TikTok Smart+）已经在各自围墙花园里包办出价、受众、版位和大部分创意组装；你的系统不是替代它，而是**通过 API 指挥它**。你真正要建的是上面那层 agent：感知效果 → 决定改什么 → 通过 API 执行，配上硬性护栏。
2. **创意就是定向。** Meta 的 Andromeda 检索引擎（2024 年 12 月）先把几千万条广告缩到几千条再排序，读的是广告本身。Meta 2025 年 4 月也说，创意多样化是找到最相关受众的最好杠杆。所以 0→1 的第一笔投资应该是**创意多样性 + 干净的转化信号**，而不是更聪明的手动定向。
3. **衡量从第一天建进去。** 平台报的 ROAS 不是因果。Stella 225 次 geo 测试的中位数增量 ROAS 是 2.31x，品牌搜索只有 0.70x。geo holdout + MMM（Meridian 是开源的）要进架构，而不是上线后再补。

另外一个清醒剂：真正高度自主的营销用例现在还不到 5%（Gartner，经 Adweek 2026 年 10 月），2028 年预计也才 15%。所以自主要一级一级上，而不是一步到位。

## 全景地图：营销 agent 到底覆盖什么

| 职能 | agent / 系统做什么 | 平台已经帮你做了什么 | 你还得自己建什么 |
|---|---|---|---|
| 创意生成 | 简报 → 角度/钩子 → 文案、图片/视频变体、本地化、改尺寸；主张必须来自产品库，不能由模型现编 | Meta Advantage+ Creative、Google AI Max 文案定制、TikTok Symphony（Smart+ 内）生成/扩展/混剪素材 | 品牌真实来源（规范、主张库）、生成提示词、多层审核（见下）、疲劳检测、变体→原型追踪 |
| 采买 / 出价 | 设/调预算、出价、上限；暂停 / 杀掉 / 放量广告组；选 campaign 结构 | Google Smart Bidding、Advantage+ 预算/版位优化、Smart+ CBO——都在黑盒里 | 决策策略：*何时*干预、用什么阈值、LLM 不能越过的硬上限 |
| 预算分配 | 按边际增量回报跨渠道 / 跨 campaign 再分配 | 只在 campaign 内部分配（如 PMax 在 Google 各渠道间分配） | 跨渠道分配器，吃的是增量修正后的回报，不是后台 ROAS； pacing + 日/周上限 |
| 受众 / 分群 | 用一手数据建/刷新人群、相似人群、意向信号；排除老客户 | Advantage+ Audience 把输入当*建议*再扩展；AI Max 无关键词匹配；Smart+ 自动定向 | 受众定义即数据、排除/抑制逻辑、合规身份（不依赖用户级追踪） |
| 衡量 / 增量 | geo holdout、lift study、MMM；把结果换算成每个渠道的修正乘数 | 平台 lift study（Meta Conversion Lift、Google Conversion Lift，用户级最低 $5,000）——平台自己给自己打分 | 独立衡量：自己的销售/CRM 为准、GeoLift / Meridian GeoX、实验校准的 MMM |
| 报告 / 洞察 | 自然语言绩效叙述、异常检测、根因、下一步行动建议 | Ads Manager / Google Ads 报表、PMax 渠道级报表（2025 年） | 统一跨渠道真实表（花费、归因收入 vs 增量收入、毛利）、每次 agent 操作的审计日志 |

两个容易浪费时间的澄清：(1) “agentic” ≠ “automated”：CPA > $X 就暂停的规则引擎是自动化；agent 是对非结构化状态（创意、市场、漏斗）推理后、经由工具在多个动作里做选择。Anthropic 的分法是标准词汇（见精读 #1）。(2) 有两个 agent 市场：替广告主在围墙花园里买量的 agent（就是你要建的系统），和买卖双方之间的 agent-to-agent 采买（AdCP / AAMP，见精读 #9），后者还早，主要在 CTV / 出版商侧。

## 三大平台今天的自动化 + 可编程的 API

| 平台 | 自动化产品 | 它决定什么 | 你还保留什么控制 | 关键事实 |
|---|---|---|---|---|
| Meta | Advantage+（Sales / App / Leads；Audience、Placements、Creative、Catalog），基于 Andromeda（检索）+ GEM（排序基础模型）+ Lattice | 谁有资格看到广告（检索短名单）、排序/竞价、预算、版位、创意变体 | 目标、预算/上限、创意素材、品牌控制、受众*建议*、排除项 | Andromeda（Engineering at Meta，2024-12-02）：检索把“几千万候选广告缩到几千条”；部分 segment 上 +6% 召回、+8% 广告质量；模型容量 10,000 倍；跑在 NVIDIA Grace Hopper + MTIA 上。Advantage+ 已是新建 sales / leads / app campaign 的默认项。 |
| Google | Performance Max（全渠道 campaign 类型）+ AI Max for Search（Search campaign 内的功能套件）+ Smart Bidding | 渠道、受众、素材组合、出价、落地页（最终 URL 扩展）、超关键词的查询匹配 | 预算、目标（tCPA / tROAS）、素材组 + 受众信号、品牌包含/排除、否定词、URL 控制、地理位置 | PMax：一个 campaign 横跨 Search、YouTube、Display、Discover、Gmail、Maps；素材按 Asset Group 组织，Google 实时组装广告。AI Max（Google，2025-05-06）：搜索词匹配（广泛 + 无关键词）、文案定制、最终 URL 扩展；Google 称通常 +14% 转化、同等 CPA / ROAS，精确/词组为主的 campaign +27%。 |
| TikTok | Smart+（Smart Performance Campaign 的继任者，2024 年 4 月）+ Symphony 生成式 AI | 定向、预算/出价（CBO）、版位、创意组合 | 目标/KPI、预算、目标、地域/语言、素材；2025 年 10 月起自动化改为**按模块开关**（定向/预算/创意/版位分别开） | TikTok（2026-05-13，2026-10-20 更新）：每个 campaign 最多 30 个广告组、每个广告组 30 个素材组、每个素材组 50 条创意；Smart+ Catalog Ads 封闭测试称 −36% CPA（TikTok 内部数据，2025）；Symphony Automation = 推荐创意 + 自动增强（含 50+ 语言配音）。 |
| 新兴 | Meta Muse for Small Business（2026-09-29）、Google Ads Advisor / Analytics Advisor（2025-11-12）、Amazon Ads Agent（2025-11） | 平台内的对话式 agent：分析账户、起草 campaign、提建议 | 人工批准：Meta 的规则是“未经你批准，什么都不发布、不发送、不花钱” | 今天这些是助手/分析师，不是自主买手——可作批准流程的 UX 参考，不可作基础设施依赖。 |

**API——你真正要编程的执行层。** 三家有广告账户就能免费用，认证都是 OAuth 2.0（Google 还要一个 developer token）。

| API | 暴露什么 | 给搭建者的提醒 |
|---|---|---|
| Meta Marketing API — `developers.facebook.com/docs/marketing-api/overview` | Campaign / ad set / ad 的增删改查、自定义受众、Ads Insights、Conversions API（服务端事件）——对象层级与 Ads Manager 相同 | Graph API，约每 4 个月一个版本（v26.0 据二手指南为 2026-07-29 发布，动手时复核）；权限 `ads_read` / `ads_management`；服务端用 system-user token；按访问层级限流。Meta 还向所有开发者开放了 **Ads MCP server**（2026 年 7 月）。 |
| Google Ads API — `developers.google.com/google-ads/api/performance-max` | Campaign、素材组、预算、出价、GAQL 报表；PMax 通过原子批量 `Mutate` 创建；AI Max 设置经 API（v21+） | Developer token + OAuth；有 Python / Java / PHP / .NET 等客户端库。*不*暴露 Smart Bidding 内部、竞价数据、对手出价。 |
| TikTok Marketing API — `business-api.tiktok.com` | Campaign / 广告组 / 广告管理、Spark Ads、视频上传、受众同步 | 需要应用审批 + 企业验证；有限流。 |
| 协议层（新） | **MCP**（Anthropic，2024 年 11 月，现属 Linux Foundation）= agent↔工具；**A2A**（Google，2025 年 4 月）= agent↔agent；**AdCP**（2025 年 10 月）= 基于 MCP / A2A 的 agent 采买任务；**IAB Tech Lab AAMP / ARTF** = agent 化 RTB 与管理协议 | Meta / Google / TikTok 优先的 0→1，先用原生 API + MCP server；AdCP / AAMP 是要盯的互操作赌注，不是起点（见精读 #9）。 |

**信号管道（不可省）：** 服务端转化事件（Meta Conversions API、Google 增强型转化 / 线下转化导入、TikTok Events API）同时喂给平台和你自己的仓库。平台优化的好坏取决于你发的转化信号质量；而你的衡量必须用自己的销售/CRM 数据，绝不能只用平台转化数。

## 系统架构

```
┌─ 数据层 ────────────────────────────────────────────────────────┐
│ 一手：CRM/订单、产品目录 + 主张库、品牌素材                     │
│ 平台：Insights / GAQL / TikTok 报表（花费、投放）               │
│ 回传信号：CAPI / 增强型转化 / Events API                       │
│ 仓库：统一的 花费 × 收入 × 毛利，geo × 天 粒度                 │
└──────────────┬───────────────────────────────────────────────────┘
               ▼
┌─ 决策层（agent + 确定性策略）───────────────────────────────────┐
│ 感知：流式/批量指标、疲劳、异常、pacing                        │
│ 推理：LLM agent（分析师 → 策略师）+ ML 模型                    │
│   （饱和曲线、MMM 边际 ROI、创意评分）                         │
│ 策略（写在代码里，不在 LLM 里）：预算上限、杀/放规则、           │
│   品牌与合规检查、审批路由                                     │
└──────────────┬───────────────────────────────────────────────────┘
               ▼
┌─ 执行层 ───────────────────────────────────────────────────────┐
│ Meta / Google / TikTok API 的工具封装（+ MCP server）          │
│ 幂等、可 dry-run 的变更；每次调用都记日志                      │
└──────────────┬───────────────────────────────────────────────────┘
               ▼
┌─ 反馈 / 评估回路 ───────────────────────────────────────────────┐
│ 短回路：平台指标 → 再分配 / 刷新创意                           │
│ 长回路：geo holdout + MMM → 修正乘数 →                        │
│   重训分配器先验、创意策略、预算拆分                           │
│ 评估：线下（历史回放、黄金集）+ 线上                           │
│   （影子模式、holdout）；每个决策都留 trace（LangSmith 等）     │
└──────────────────────────────────────────────────────────────────┘
        护栏包住所有层：硬上限、白名单、主张核验、人工审批关口
```

## 自主阶梯 L0–L4：一次上一级，关口明确

| 阶段 | agent 做什么 | 人做什么 | 升下一级的退出标准 |
|---|---|---|---|
| L0 手动 | 什么都不做；只有看板 + 告警 | 所有决策与执行 | 数据层可靠；一个渠道经 API 上线（只读） |
| L1 辅助（推荐） | 起草动作 + 理由；不写库 | 在应用里逐条批准/拒绝 | >90% 的建议被接受或被正确拒绝；评估通过 |
| L2 半自主（有界写入） | 在上限内执行预批准的动作类（如 CPA > 3× 目标则暂停、每天预算移动 ≤15%） | 每天看队列；批准新创意 / 加预算 | 4–8 周无护栏 breach；影子模式结果一致 |
| L3 自主（有监督） | 完整回路，包括创意生成 + 上线，在预算包络与品牌锁内 | 定目标/上限、每周复盘、处理异常 | 增量验证过的回报；应急预案演练过 |
| L4 完全自主 | 在包络内无限期运行；只上报异常 | 治理：目标、上限、品牌真实、审计 | ——（愿景；今天高度自主的营销用例不到 5%，Gartner 经 Adweek 2026 年 10 月） |

**0→1 的建议搭建顺序：**(1) Meta 只读数据 + 汇报 agent；(2) 确定性规则（暂停/杀/pacing）+ 人工批准；(3) 带五层审核的创意生成管线；(4) 基于增量修正回报的预算分配器；(5) 用同一渠道抽象接 Google、再接 TikTok；(6) 按阶梯一次放宽一个动作类。不要一上来就搞能花钱的自由多 agent 群。

## 搭建者必须知道的关键概念

- **Workflow vs agent（Anthropic，2024 年 12 月）：** workflow = LLM/工具跑在*预定义的代码路径*上；agent = LLM *动态指挥*自己的流程与工具调用。五种可组合的 workflow 模式——提示链、路由、并行、 orchestrator-workers、evaluator-optimizer——覆盖大多数真实需求。原则：用能通过评估的最简单模式；只有在无法把路径写死、但*能验证进展*的开放任务上才用完整 agent。先直接调 LLM API；框架（LangGraph、CrewAI、Strands、Claude Agent SDK）加的抽象会遮住提示词、让调试更难。
- **工具设计 / agent-计算机接口：** 工具要像 UI 一样认真设计——精确的名字、schema、文档、类型化/结构化输出、幂等键。LLM 绝不能决定*要不要花钱*：上限、数量、发布由代码决定；LLM 只在边界内做语义工作（排序、起草）。
- **评估就是产品：** 线下评估（黄金创意集、历史回放）、线上评估（影子模式——agent 提议、人执行、再比较）、决策 trace。量不出 agent 的决策质量，就不敢放宽自主。Anthropic 的三原则：简单、透明（展示规划步骤）、认真设计工具。
- **幻觉 / 主张风险：** 模型会很自信地编价格、规格、折扣和统计数字。缓解办法是检索 grounding（只从产品/主张库生成）+ 主张核验层（对不上真实来源就拒掉）+ 抽样批量审计。见精读 #12 的五层管线。
- **品牌安全：** 两层——*适宜性*（广告出现在哪里；平台品牌安全模块）和*身份*（创意是否 on-brand：语气、色值、字体、主张）。放量时真正的杀手不是灾难性错误，而是大量轻微跑偏的变体在一个季度里稀释品牌。新解法：`brand.json`——品牌自有域名上的一份机器可读品牌真实文件（AdCP 3.0）——见精读 #11。
- **预算护栏（确定性、写在代码里、放在 LLM 外面）：** 硬性日/账户上限、单次改动上限（如每天预算 ±15%）、最低 ROAS / 最高 CPA 底线触发自动暂停、杀规则（如 3× 目标 CPA 且无转化）、pacing 告警、全局 kill-switch。每次变更都记 actor、理由和前后状态。先按这个设计失败模式：曾有报道 Meta campaign 在第一小时花掉日预算 150% 以上（2025 年 11 月）。
- **增量 vs 归因 vs MMM——三角验证，别只选一个：**
  - *归因*（last-click / MTA）：哪些触点认领了转化。适合日常运营；系统性高估再营销和品牌搜索；受隐私变化影响（仅浏览器追踪会漏 30–40% 的转化）。
  - *增量*（geo holdout、受众 holdout、平台 lift）：花费*导致*了什么，对照反事实。预算决策的金标准；慢（数周）、粗（渠道级）。
  - *MMM*（Meridian、Robyn）：随时间对销售额做花费回归，带饱和/adstock；常开、跨渠道、隐私耐用（聚合数据）。除非用实验结果做先验校准，否则只是相关性。
  - 操作规则：日常用归因、每季度每大渠道跑一次 geo 测试（产出 = 平台 ROAS 的修正乘数）、跨渠道分配用 MMM——还要重测，因为乘数会随平台变化而漂移。
- **创意就是定向；疲劳就是时钟：** Andromeda 之下，多样化创意（不同角度、钩子、格式、受众）是触达的主要杠杆；近似重复算一条创意。创意死得快——2026 年一项对 290 万条 Meta 广告的分析称中位寿命 3 天——所以系统需要持续刷新节奏，不是一次性批量。
- **学习期 / 信号质量：** 平台每个广告组每周约需 50 个转化事件才能出学习期；改动会重置学习期。agent 必须批量改动、尊重学习窗口、绝不频繁改设置。干净、去重的服务端转化信号比任何出价技巧都重要。

## 值得知道的玩家、工具与开放框架

*提醒：这个领域变化快、厂商说法多；下面的融资/表现数字来自媒体/厂商口径，未独立核实。按原型来读，别当背书。*

| 原型 | 例子 | 做什么 |
|---|---|---|
| Agent 化采买平台 | Apostra（ex-Scope3，AdCP 发起方）——跨 Meta / Google / Pinterest / Reddit / Spotify / ChatGPT 的 agent-to-agent 采买，到 2026 年 10 月 3 个月约 $1.2m；PubMatic AgenticOS（2026 年 1 月）；Magnite 买方 agent + 编排层；The Trade Desk 对话式界面路由到专家 agent；Kargo Project Kera | 经由 agent 规划/谈判/执行媒体；目前多在 CTV / 程序化，预算小、仍有人盯着 |
| 跨渠道 AI 广告管理员 | Synter Media（MCP server，27 个平台，含 Google / Meta / TikTok / TTD）；MINT.ai；Abijany（Meta / Google / TikTok / YouTube 官方 API，护栏优先） | 一个系统操作多个围墙花园——最接近你要建的东西；重点学它们的护栏/审批 UX |
| 创意生成 agent | Runway Ads（为 Meta / Google / TikTok 本地化的视频变体）；Celtra（创意自动化 + 治理，brand.json 合著方）；VIMDRIVE / VIMC01；AdCreative.ai、Jasper、Writer | 规模化生产变体；差异化现在是治理/主张核查，不是生成速度 |
| 衡量 / 增量 | Haus（分析过 640 次 Meta 测试；增量归因）、Stella / Measured（geo 基准）、Northbeam、Google Meridian + GeoX（开源）、Meta GeoLift（开源，2021）、Meta Robyn（开源 MMM） | 分配器依赖的衡量栈；GeoLift / Meridian 免费，小团队也能跑 |
| Agent 框架（开源） | LangGraph（有状态图，适合长跑广告回路）、CrewAI（按角色分工：分析师/策略师/执行）、AutoGen（多 agent 对话）、MCP（工具标准）、n8n / Temporal（可靠执行/调度——花钱的路径放在确定性 workflow 工具里，别放在 LLM 回路里） | 编排 + 工具层；配 LangSmith / 可观测性看 trace |
| 标准组织 | AgenticAdvertising.org / AdCP（brand.json、AdCP 3.0）、IAB Tech Lab AAMP / ARTF、Prebid.org（参考销售 agent） | agent-to-agent 采买正在标准化的地方；盯着，别急着在围墙花园采买上押注 |
| 平台原生 agent | Meta Muse for Small Business、Google Ads Advisor / Analytics Advisor、Amazon Ads Agent | 各平台内的助手；审批制；作 UX 参考 |

## 无法核实 / 待确认

- **API 版本号变得快。** Meta Marketing API v26.0（2026 年 7 月）与 Google Ads API 版本来自索引/二手来源；动手时以官方文档为准。TikTok Marketing API 的限流/版本（v1.3）同样是索引来源。
- **厂商表现数字未经核实。** 所有“+14% / +22% / −36% CPA”都是平台自己的测试；独立结果一贯更 mixed（例如 Haus：Advantage+ 比手动多报了 12 个百分点；58% 的品牌在手动上看到更高 iROAS）。没找到 Advantage+ vs PMax vs Smart+ 在相同预算下的独立横评。
- **Meta GEM 细节**（2025 年 11 月 10 日工程文章）与 **Meta 创意多样化指引**（2025 年 4 月 22 日）经搜索/二手来源佐证，但未直接打开；Andromeda 原文已核验，支撑核心论点。
- **平台原生 agent 的价格/可用性**（Meta Muse 档位、Google Ads Advisor 推广进度）仍在变、部分仅限美国；框架不依赖它们。
- **本次调研没做的事：** 没在 Ads Manager / Ads API 里实测、没有账户级数据、没有核实任何初创公司的融资或客户结果（媒体报道之外）。
