# Content Pipeline — 五环节内容生产流水线（Agent Skill）

> 定时找选题 → 自动攒素材 → 一键出初稿 → 排版成稿 → 推送审稿。
> 单篇产出从约 3.7 小时压到约 25 分钟，**体力活全自动，判断力留给人**。

![License](https://img.shields.io/badge/license-MIT-blue)
![Skill](https://img.shields.io/badge/format-Agent%20Skill-brightgreen)
![Version](https://img.shields.io/badge/version-1.0.0-orange)

作者：AsLSX99

## 这是什么

一个成品级、**平台无关**的 Agent Skill：把公众号（也适用于知乎/博客/Newsletter/小红书）的"选题→素材→初稿→排版→分发"提炼成五环节流水线，每环节交付三件套——**参数化提示词 + 质量门禁 + 失败回退**，并附五能力接线表，可运行在任何具备"定时、知识库、生成、文件、通知"能力的 Agent 上。

来自真实实践的基线数据（分环节计时）：

| 环节 | 以前 | 现在 |
|------|------|------|
| 找选题 | 60 分钟 | 0（自动清单）+ 3 分钟挑 |
| 找素材 | 40 分钟 | 0（结构化摘要） |
| 写初稿 | 90 分钟 | 20 分钟（只改不写） |
| 排版 | 30 分钟 | 1 分钟 |
| **合计** | **约 3.7 小时** | **约 25 分钟**（不含人工审稿发布） |

## 设计红线

1. **AI 出"骨架+毛坯"，人做"装修"**——初稿必经 15–20 分钟人工改稿（删 AI 腔 / 换真实经历 / 砍长句），这一步省不掉也不该省。
2. **审稿与发布永远是人的动作**——流水线止步于"排好版的成品 + 交付通知"。跳过审稿的号，活不长。
3. **素材必须带来源**——无来源的素材摘要视为门禁失败。
4. **缺口显式传递**——素材不足就标注 ⚠ 并保留占位，静默编造是流水线最重的罪。

## 解决什么问题

| 症状 | 对应设计 |
|------|---------|
| 找选题刷一小时、给 20 个没法挑 | P1 写死数量 + 评分排序 + G1 门禁 |
| 写的时候没料、现场翻 40 分钟 | 资料库随手存 + 全文检索 + 结构化摘要（观点/数据/案例带来源） |
| AI 初稿一股机器味 | G3 禁词断言 + 改稿清单三件事（判断力红线） |
| 每次排版风格不一样、挑模板半小时 | Style Profile 一次性建档 + G4 逐项比对 |
| 稿子好了没人知道 | 分发通知（标题带选题名 + 门禁结果 + 缺口） |
| 流水线卡死在半路 | 每环节"重跑×2 → 降级+标注"，显式缺口继续跑 |
| 提示词换个领域就废 | 全部参数化（领域/字数/口吻/数量从 Spec 取值） |
| 换个工具就搭不起来 | 五能力模型 + 三类 Agent 接线表（07 文档） |

## 仓库结构

```
.
├── README.md
├── LICENSE
├── content-pipeline/                # Skill 本体
│   ├── SKILL.md                    # 主工作流（五环节 + 三件套 + 判断力红线）
│   ├── references/
│   │   ├── 01-topic.md             # 选题：提示词/评分/G1
│   │   ├── 02-research.md          # 素材：入库规范/摘要/G2/版权纪律
│   │   ├── 03-drafting.md          # 初稿：提示词/改稿清单/G3
│   │   ├── 04-formatting.md        # 排版：Style Profile/G4
│   │   ├── 05-distribution.md      # 分发：通知/发布检查表/职责边界
│   │   ├── 06-quality-gates.md     # G1–G4 总表 + 可执行硬规则
│   │   ├── 07-platform-adapters.md # 五能力模型 + 接线表（应用与所有 Agent）
│   │   └── 08-iteration.md         # 数据回流/提示词版本化/故障对照/基线测量
│   └── assets/
│       ├── templates/
│       │   ├── prompt-pack.md      # 5 条参数化提示词 + 禁词表
│       │   └── pipeline-spec.md    # 总配置表 + 依赖传递铁律
│       └── examples/
│           └── full-run.md         # 一天流水线完整跑通示例
└── evals/
    └── evals.json                  # 测试用例
```

渐进式披露：`SKILL.md` 只承载主流程与三件套结构，环节细节执行到才读。

## 安装

```bash
# 任何支持 Agent Skill 的运行时（项目级）
cp -r content-pipeline <你的项目>/.skills/

# 用户级
cp -r content-pipeline ~/.skills/

# Claude Code
mkdir -p ~/.claude/skills && cp -r content-pipeline ~/.claude/skills/
```

确认 `SKILL.md` 位于 `content-pipeline/` 根目录。

## 使用

直接说人话触发：

- "帮我搭一套公众号自动出稿流水线，领域是 AI 工具"
- "我每篇要 4 个小时，帮我优化写作流程"
- "把这篇效率教程提炼成一个可复用的 SKILL"
- "素材太难找了，做选题到排版的自动化"

执行顺序：填 `pipeline-spec.md` 总配置 → 逐环节落地三件套 → 按 07 文档接线 → 先跑通一天 → 按 08 文档迭代。

## 适配的 Agent

五能力模型（C1 定时 / C2 知识库 / C3 生成 / C4 文件 / C5 通知）+ 最小可运行集（C3 + 人工触发）。07 文档给出三类环境的接线表：带自动化任务的平台（WorkBuddy 类）、代码执行型 Agent（Claude Code 类）、纯对话 Agent。

## 测试用例

`evals/evals.json` 含 3 个测试提示词（从零搭建、已有提示词升级、翻车修复）。

## 许可

[MIT](LICENSE)

## 作者

GitHub: [AsLSX99](https://github.com/AsLSX99)
