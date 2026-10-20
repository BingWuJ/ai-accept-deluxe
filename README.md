<h1 align="center">ai-accept-deluxe</h1>

<p align="center"><b>按豪华流程验收 AI 代码 · Multi-model adversarial acceptance for AI-generated code</b></p>

<p align="center">
  <img alt="Type" src="https://img.shields.io/badge/type-AI%20Skill-1f6feb?style=flat-square" />
  <img alt="Format" src="https://img.shields.io/badge/format-SKILL.md-2ea043?style=flat-square" />
  <img alt="Models" src="https://img.shields.io/badge/reviewers-2%20(adversarial)-8957e5?style=flat-square" />
  <img alt="Requires" src="https://img.shields.io/badge/requires-codex%20CLI-orange?style=flat-square" />
</p>

<p align="center">
  <a href="#简体中文">简体中文</a> | <a href="#english">English</a>
</p>

---

## 简体中文

### 这是什么

一份给 AI 编码代理用的 **AI 代码验收方法论**。代码可以许愿生成，质量不能——这个 skill 把你的愿望变成可验收的边界，并用**第二个模型的对抗审查**把 AI 自己写的代码挑出错。

它假定你的环境里有多个模型（主 agent + `codex` CLI），因此可以执行完整的对抗审查。

- **文档驱动** —— 先有 PRD，才有任务清单，才有验收标准
- **多模型对抗** —— 每条改动都交给另一个模型以对抗立场审查
- **复杂度惩罚** —— 以「优雅 / 扩展性」换来的抽象一律按负资产对待
- **进度可续跑** —— `TASKS.md` 是唯一进度真相，中断后从第一个未勾项继续
- **签字不外判** —— 最终验收由你本人走查签字，agent 只准备材料

### 铁律（四条）

1. **禁止过度设计** —— 无论实现还是 review 意见，都只做要求的：不加抽象层、不做预留扩展、不得以「建议增强」为由打回。意见只分两类：**需求没实现 / 实现有缺陷（blocking）** 与 **不 blocking**。「可以更优雅」「建议抽象」「将来可能需要」一律不算问题。
2. **串行，禁止并行** —— 从上到下一条一条做。禁止 worktree、禁止新建分支、禁止 subagent、禁止并行任务。
3. **任务清单是唯一进度真相** —— 续跑先读 `TASKS.md`，从第一个未勾选项继续，禁止跳项、禁止重排。
4. **验收签字不能外包** —— 最终验收由用户本人执行，agent 只准备验收材料。

### 六步工作流

| 步骤 | 做什么 | 落盘产物 |
| --- | --- | --- |
| 1. 许愿 | 读 PRD；没有就起草，交你逐条确认后才开工 | PRD |
| 2. 拆清单 | 按 PRD 章节拆成**完成标准可判定**的有序清单 | `TASKS.md`、`session-log.md` |
| 3. 逐条实现 | 一次只做一条，不加抽象层、不做预留扩展 | 代码 / 测试 |
| 4. 立即对抗审查 | 每条完成即送 `codex`，只认四类 blocking；同条最多 3 遍，3 遍不过停下交你裁决 | 审查记录 |
| 5. 暴力测试 | 穷举破坏性用例；问题先固化为回归测试再修 | 回归测试 |
| 6. 交付与走查 | 代码 + 用户手册 + 验收清单；**你本人**从零走通并签字 | 手册、验收清单 |

> **token 预算提醒**：追求工程质量时，70%~80% 的 token 应花在第 5 步暴力测试，而不是写代码。

### 送审前置检查（关键）

第 4 步送审前必须先确认：`codex --yolo` 可用，且其配置确为 **GPT 5.6 sol max**。

- codex 不可用，**或**无法确认档位 → **停下报告用户，禁止静默降级为自查**
- 档位名对不上（配置里是别的模型，或只能靠迁移表 / 产品线推断「认定等价」）→ 同样停下
- **替换评审模型必须由用户明示授权**；也不得为凑档位擅自改全局 `codex` 配置
- 真实 CLI 能跑通，不等于替换已获授权

这条是豪华版与标准版的本质分界：没有第二个强模型做真对抗，豪华版就退化成了标准版。

### 安装

把 `SKILL.md` 放进你的代理读取 skill 的目录，目录名建议与 skill 同名：

```bash
# 全局（跨项目可用）
mkdir -p ~/.agents/skills/ai-accept-deluxe
curl -fsSL https://raw.githubusercontent.com/BingWuJ/ai-accept-deluxe/main/SKILL.md \
  -o ~/.agents/skills/ai-accept-deluxe/SKILL.md

# 或仅当前项目
mkdir -p .skills/ai-accept-deluxe
curl -fsSL https://raw.githubusercontent.com/BingWuJ/ai-accept-deluxe/main/SKILL.md \
  -o .skills/ai-accept-deluxe/SKILL.md
```

代理会在描述匹配「多模型完整对抗验收」时自动加载；也可显式指名调用。

### 与标准版的关系

| | **豪华版**（本仓库） | [标准版](https://github.com/BingWuJ/ai-accept-standard) |
| --- | --- | --- |
| 评审方 | 另一个模型（`codex` CLI） | 同模型的冷启动会话 / 结构化自查 |
| 对抗强度 | 真对抗，有独立立场 | 弱于双模型，靠更严的用户走查兜底 |
| 评审方不可用 | 停机报告，**禁止降级** | 冷启动不可行时降级为结构化自查 |
| 适用环境 | 有第二模型可用 | 单模型环境 |

### 边界

- 这是**流程** skill，不替你写业务需求；PRD 不确认就不开工。
- 不适用于「随便看看、快速改一行」的场景——它的成本在对抗审查和暴力测试上。
- 自行调用会真实占用两个模型的额度；第 5 步尤其费 token。

---

## English

### What is this

An **AI code acceptance methodology** for coding agents. Code can be wish-generated; quality cannot. This skill turns your wish into verifiable boundaries, then uses **a second model as an adversarial reviewer** to break the code the AI just wrote.

It assumes a multi-model environment (main agent + `codex` CLI), so the full adversarial loop is available.

- **Document-driven** — PRD first, then task list, then acceptance criteria
- **Multi-model adversarial review** — every change goes to another model instructed to attack it
- **Complexity penalty** — abstractions bought with "elegance / extensibility" count as liabilities
- **Resumable progress** — `TASKS.md` is the single source of truth; resume from the first unchecked item
- **Signature is not delegable** — you walk through and sign off; the agent only prepares the material

### Iron rules (four)

1. **No over-engineering** — implement only what was asked: no abstraction layers, no speculative extension, and review may not reject a change for "suggested enhancement". A finding is either **requirement missing / implementation defect (blocking)** or **not blocking**. "Could be more elegant", "suggest an abstraction", "might be needed later" never count.
2. **Serial, never parallel** — top to bottom, one item at a time. No worktrees, no new branches, no subagents, no parallel tasks.
3. **The task list is the only progress truth** — on resume, read `TASKS.md` and continue from the first unchecked item. No skipping, no reordering.
4. **Sign-off cannot be outsourced** — the final acceptance is performed by the user; the agent only prepares material.

### The six steps

| Step | What happens | Artifact |
| --- | --- | --- |
| 1. Wish | Read the PRD; if absent, draft one and get your item-by-item confirmation before starting | PRD |
| 2. Break down | Split into an ordered list with **decidable completion criteria** | `TASKS.md`, `session-log.md` |
| 3. Implement | One item at a time; no abstraction, no speculative extension | code / tests |
| 4. Adversarial review | Each item goes straight to `codex`, only four blocking classes; max 3 rounds per item, then stop and escalate to you | review records |
| 5. Brutal testing | Enumerate destructive cases; file a regression test before fixing | regression tests |
| 6. Deliver & walk through | code + user guide + acceptance checklist; **you** run it from scratch and sign | guide, checklist |

> **Token budget**: for real engineering quality, 70–80% of tokens belong to step 5, not to writing code.

### Pre-review gate (the critical part)

Before step 4, verify that `codex --yolo` is available and configured as **GPT 5.6 sol max**.

- Codex unavailable **or** tier unconfirmable → **stop and report; silent downgrade to self-review is forbidden**
- Tier does not match (a different model, or equality only "inferred" from a migration table / product line) → stop as well
- **Replacing the reviewer model requires explicit user authorization**; never edit the global `codex` config just to make the tier fit
- A CLI that runs does **not** mean the substitution is authorized

This is the boundary between deluxe and standard: without a second strong model doing real adversarial review, deluxe collapses into standard.

### Install

Drop `SKILL.md` where your agent loads skills, in a directory named after the skill:

```bash
# Global (all projects)
mkdir -p ~/.agents/skills/ai-accept-deluxe
curl -fsSL https://raw.githubusercontent.com/BingWuJ/ai-accept-deluxe/main/SKILL.md \
  -o ~/.agents/skills/ai-accept-deluxe/SKILL.md

# Or this project only
mkdir -p .skills/ai-accept-deluxe
curl -fsSL https://raw.githubusercontent.com/BingWuJ/ai-accept-deluxe/main/SKILL.md \
  -o .skills/ai-accept-deluxe/SKILL.md
```

The agent loads it automatically when the request matches "full multi-model adversarial acceptance", or you can invoke it by name.

### Relation to the standard edition

| | **Deluxe** (this repo) | [Standard](https://github.com/BingWuJ/ai-accept-standard) |
| --- | --- | --- |
| Reviewer | Another model (`codex` CLI) | Cold-start session of the same model / structured self-check |
| Adversarial strength | Real adversarial, independent stance | Weaker; backed by a stricter user walkthrough |
| Reviewer unavailable | Stop and report, **downgrade forbidden** | Falls back to structured self-check |
| Fits | Environments with a second model | Single-model environments |

### Boundaries

- This is a **process** skill; it does not invent your business requirements. No confirmed PRD, no work.
- Not for "just quickly change one line" — its cost lives in adversarial review and brutal testing.
- A real run consumes two models' quota; step 5 is the expensive one.

---

<p align="center">
  <sub>Skill body: <a href="./SKILL.md">SKILL.md</a> · Companion: <a href="https://github.com/BingWuJ/ai-accept-standard">ai-accept-standard</a></sub>
</p>
