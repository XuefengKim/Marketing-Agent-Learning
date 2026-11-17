# 首批精读（12 篇，2026-11-17）

首批 sourcing：全球来源，12 篇在调研时逐条打开核验过链接。中文摘要与“为什么读”由调研提炼；厂商自报的提升数字均为官方口径，不是实测保证。

---

## 1. Building effective AI agents — Anthropic，2024-12-19

https://www.anthropic.com/engineering/building-effective-agents

Anthropic 划出这条领域的标准线：*workflow*（LLM/工具跑在预定义的代码路径上）与 *agent*（LLM 动态指挥自己的流程与工具调用）的区别；给出五种可组合的 workflow 模式——提示链、路由、并行、orchestrator-workers、evaluator-optimizer；并建议先直接调 API、保持设计简单透明、认真设计工具、先评估再加复杂度。（页面现已注明：自 2024 年 12 月以来工具格局变了，模式没变。）

**为什么读（0→1 搭建者）：** 这是设计词汇，能帮你避免第一天就搭一个自由奔跑的“agent 群”——广告系统的大部分应该是带关口的 workflow，真正的 agent 只留给能验证的开放任务。

---

## 2. Meta Andromeda: Supercharging Advantage+ automation with the next-gen personalized ads retrieval engine — Engineering at Meta，2024-12-02

https://engineering.fb.com/2024/12/02/production-engineering/meta-andromeda-advantage-automation-next-gen-personalized-ads-retrieval-engine/

Meta 自己的工程复盘：检索是广告投放的第一阶段，把几千万候选广告缩到几千条再排序；定制深度网络跑在 NVIDIA Grace Hopper（和 MTIA）上，模型容量 10,000 倍，部分 segment 上 +6% 检索召回、+8% 广告质量；层级索引的设计目标就是吸纳 Advantage+ / 生成式 AI 带来的创意量爆炸。

**为什么读：** 它解释了*为什么*创意现在是定向输入、为什么你的系统必须批量生产真正多样化的创意——决定谁能看到广告的是检索层，不是你的受众设置。

---

## 3. Introducing AI Max for Search campaigns — Google（The Keyword），2025-05-06，作者 Brian Burdick

https://blog.google/products/ads-commerce/google-ai-max-for-search-campaigns/

Google 发布 AI Max，作为 Search campaign 内的一键套件：搜索词匹配（广泛 + 无关键词）、文案定制（自动生成标题/描述）和最终 URL 扩展，带品牌/地域控制与更丰富的搜索词报告；Google 称通常 +14% 转化、同等 CPA / ROAS（精确/词组为主的 campaign +27%），发布案例有 L'Oréal（转化率 2 倍、单次转化成本 −31%）和 MyConnect（+16% 线索）。

**为什么读：** Google 这边的标准说法——关键词列表越来越不重要，落地页/素材质量与意图覆盖越来越重要；同时划清了你的系统还能通过程序控制什么、什么已经归 Google 管。

---

## 4. Smart+: AI-Powered Solution To Maximise Ad Campaign Results — TikTok for Business，2026-05-13（2026-10-20 更新）

https://ads.tiktok.com/business/en-GB/blog/smart-plus-ai-performance-solution

TikTok 的 Smart+ 官方总览：定向、预算/出价（CBO）、版位、创意全自动化，覆盖 Traffic、App、Lead Gen、Sales；现在改为*按模块开关*而非全有全无；每个 campaign 最多 30 个广告组、每个广告组 30 个素材组、每个素材组 50 条创意；Smart+ Catalog Ads（封闭测试称 −36% CPA）与 Symphony Automation（推荐创意、自动增强，含 50+ 语言配音）。

**为什么读：** TikTok 是多渠道搭建的第三条腿，也是唯一一家往回走、给回颗粒度控制的平台——你的系统也该把自动化做成按模块的开关，而不是一个总开关。

---

## 5. Building an Agentic MarTech Stack: A Technical Deep Dive — Medium，Brian James Curry，2026 年 2 月

https://medium.com/@brian-curry-research/building-an-agentic-martech-stack-a-technical-deep-dive-07d51931336a

全栈蓝图：编排（LangGraph / CrewAI）、agent 层（campaign 优化、内容生成、受众智能、预算分配、监控、A/B 测试）、推理层（LLM + 向量检索 + ML 模型）、感知层（Kafka / Flink / Redis）、行动层（Google / Meta / TikTok / 邮件 / CMS / CRM）与可观测性；附 LangGraph 代码、感知–推理–行动回路、分阶段上线（先上影响最大、风险最低的 agent；6 周内预算占比 10% → 50% → 100%）。*提醒：* 文中“30–40% 更低 CPA”只归因于未具名的早期采用者。

**为什么读：** 这份清单里最接近能照着搭的参考架构——抄它的分层与上线纪律，别抄它没出处的表现数字。

---

## 6. The PPC marketer's guide to Google's Meridian — Search Engine Land，2026-10-09

https://searchengineland.com/google-meridian-ppc-guide-494563

Meridian（2024 年推出，2025 年初全面开放）是 Python、完全贝叶斯、开源的 MMM：geo 级分层建模、用 Google Query Volume 控制自然需求、时变截距，关键机制是用增量实验结果校准先验；指南给出 5 步实施（数据准备、adstock/饱和设定、MCMC 训练/诊断、校准、场景优化），并诚实提醒数据干净程度与技术门槛。

**为什么读：** MMM 是分配器的长回路大脑——这是用因果的、隐私耐用的数据做预算决策的实用入门，而且代码看得见，不是个厂商黑盒。

---

## 7. Incrementality Testing on a Small Budget: The Geo Holdout Method (2026) — Orbit，2026-09-25

https://orbitllm.com/resources/guides/incrementality-testing-geo-holdout

小预算、不用厂商的 geo holdout 配方：用未触达市场建合成控制、花钱前先做功效分析（GeoLift 或 Meridian GeoX，都是免费/开源）、hold out 2–5 个市场至少一个购买周期加一段后测期，产出一个*乘数*（增量 ROAS ÷ 平台 ROAS）去修正此后每一个平台数字；并记录了陷阱——中途读结果（Haus：Advantage+ 中期看好 9%，结束时差 12%）、泄露、测试太短——以及新的低成本门槛（Google 用户级 lift 起步 $5,000）。

**为什么读：** 这里最可操作的一篇衡量文章——0→1 的搭建者用区域销售数据加免费工具就能跑，而它产出的乘数正是跨渠道预算分配器需要的输入。

---

## 8. How to Test For Incremental Impact: Paid Media & Incrementality Testing — Stella，2026 年 6 月更新

https://www.stellaheystella.com/blog/how-to-test-for-incremental-impact-paid-media-incrementality-testing

Stella 定义增量收入（实际减反事实），按可审计性给五种测试设计排序（geo holdout、受众 holdout、平台 lift、switchback、实验校准的 MMM），并公布 225 次 DTC 测试基准（2024 年 8 月–2025 年 12 月）：中位 iROAS 2.31x（IQR 1.36–3.24x），品牌搜索 0.70x，Meta 2.92x，Performance Max 2.98x，TikTok 0.94x——同时明确提醒样本是自选的、平台 ROAS 可能*两个方向*都误导（46 次反向 holdout 里 Meta 平均比上报多 21% 增量）。

**为什么读：** 既给决策框架（测什么、用什么 KPI——总收入、新客、毛利会改变答案），又给唯一公开的跨渠道 iROAS 基准，用来校准你的第一次测试。

---

## 9. A Guide to the New, Wide World of Agentic Advertising and Commerce Protocols — Adweek，2026-05-28

https://www.adweek.com/media/a-guide-to-the-new-wide-world-of-agentic-advertising-and-commerce-protocols/

协议栈地图：基础设施（MCP，2024 年 11 月；A2A，Google，2025 年 4 月）、agent 化广告（AdCP，2025 年 10 月，基于 MCP，由 AgenticAdvertising.org 监管；IAB Tech Lab 的 ARTF / AAMP 作为 RTB 现代化的另一路线；Agentic Audiences 做隐私安全的信号交换）、agent 化商务（OpenAI / Stripe ACP、Google UCP、AP2、Visa TAP、x402）。

**为什么读：** 写一行集成代码之前，先知道每个标准占哪一层——0→1 先用原生 API / MCP，这个地图告诉你该盯什么、不该把公司押在什么上。

---

## 10. ADWEEK Tech Advantage: AI Agents Are Increasingly Getting Their Hands on Ad Dollars — Adweek，2026-10-06

https://www.adweek.com/programmatic/adweek-tech-advantage-ai-agents-are-increasingly-getting-their-hands-on-ad-dollars/

落地的现状报告：Apostra 的 agent 在 3 个月里处理了约 $1.2m 采买；Rouge Care 称 agent 在 PubMatic AgenticOS 上 $25k CTV 拿到 5x ROAS；The Trade Desk、Magnite、Kargo、Runway 都在出 agent 产品——但“没人做到规模化”，高度自主部署在 2025 年不到营销用例的 5%（Gartner；2028 年预计 15%），开放问题是问责（“agent 把 $10m 重新分配后效果掉了，谁负责？”）和协议碎片化。

**为什么读：** 给 0→1 计划的祛魅剂——真金白银在动，但量小、有人监督；这验证了框架里的分阶段自主阶梯，也提醒你从一开始就设计问责/审计 trace。

---

## 11. Agentic Advertising Without Governance Is Just Chaos at Scale — Celtra（Pia Malovrh），2026-04-21

https://celtra.com/blog/agentic-advertising-without-governance-is-just-chaos-at-scale/

Celtra 产品负责人认为瓶颈不是 AI 能力，而是*治理缺口*：今天的创意 agent 没有权威的品牌真实来源，只能爬网站、猜颜色字体语气——然后人工 QA 被动扩大去兜底，agent 化的意义就没了。解法是 `brand.json`，品牌自有域名上的一份机器可读品牌身份文件（AdCP 3.0 的一部分），让治理在生成*之前*就生效，人变成“治理架构师”而不是纠错员。

**为什么读：** 它把品牌安全从检查清单重构成基础设施——对你的系统来说，对等物就是一份生成时必须解析的机器可读品牌/主张来源，而不是让人逐条审变体。

---

## 12. Brand-safe AI creative — what we learned from 50,000 ad variants — VIMDRIVE，2026-04-08

https://vimdrive.com/blog/brand-safe-ai-creative-50000-variants/

一个创意 agent（VIMC01）在量产下的实践复盘：品牌安全是五层管线——(1) 确定性硬约束，(2) 微调过的品牌语气分类器，(3) 对产品数据库的主张核验，(4) 抽样批量审计（10–15%，分层抽样），(5) 在*原型*层面锁品牌（50 个锁定的原型覆盖 5,000 个变体）——另有反直觉发现：胜率要到约 1,000 个变体才拐弯、5,000 个之后走平；审计层是“方向盘”，拿掉它会产出单条看都行、整体同质稀释的创意。

**为什么读：** 这套里最具体的护栏设计——告诉你生成周围该建什么、量什么（各层的通过率）、以及到底多大的量才值得生产。
