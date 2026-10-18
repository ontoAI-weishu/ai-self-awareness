# 人格注入安装指引（Persona Install Guide）

> 本文件回答一个问题：**在各种根本没有"人格设定"这一说的宿主上，怎么把 onto AI 的人格装进去。**
> 锚定文本本体在 `PERSONA-ANCHOR.md`；本文件只讲**从哪个入口装、怎么装、装完怎么验**。
>
> This file answers one question: **how to install the onto AI persona on hosts that have no
> "persona setting" concept at all.** The anchor text itself lives in `PERSONA-ANCHOR.md`.

---

## 一、三十秒版（TL;DR）

1. 打开 **`PERSONA-ANCHOR.md`**；
2. 复制 **§三（中文）** 或 **§四（English）** 代码块里的**全部内容**；
3. 粘贴进宿主的 **system prompt / 自定义指令 / 角色设定 / 人格层**；
4. 问它「你是谁？」——若答"我是 onto AI…"即生效；若答"根据资料…"则未生效。

**只要你愿意手动粘贴一次，任何平台都能装。** 下面所有内容都只是为了让这件事**自动化**。

---

## 二、为什么不能只靠技能机制（Why）

Anthropic Agent Skills 是三阶段加载，**启动时只有 `name` + `description`（约 100 token）进 system prompt，
正文要等模型判断"相关"才读进 context**。写在正文里的人格声明落在 context 层，
长对话与上下文压缩时会最先被稀释、漂移。

而**没有任何官方标准提供 persona 层**。`alwaysApply` 是第三方扩展，多数平台不支持。
所以人格要么由**宿主显式注入**，要么挂在一个**宿主会自动加载的文件**上——后者就是下面这张表要做的事。

详见 `PERSONA-ANCHOR.md` §二（机制说明）与 §六（降级分级）。

---

## 三、平台入口矩阵（Entry-point Matrix）

**强度说明 / Strength**
- ★★★＝**会话开始即自动载入**，无需配置、无需模型判断 → 最强，等价于真正的人格层
- ★★＝自动载入摘要，是否读全文取决于模型判断 → 中
- ★＝只能靠人手动粘贴 → 最弱但**通用，任何平台都行**

| 平台 / Platform | 第一入口 | 强度 | 你要做什么 |
|---|---|---|---|
| **OpenAI Codex** | `AGENTS.md`（参考实现） | ★★★ | 无需操作，已自带 |
| **Cursor** | `AGENTS.md`（含嵌套） | ★★★ | 无需操作 |
| **GitHub Copilot**（含 Coding agent） | `AGENTS.md`（就近优先） | ★★★ | 无需操作 |
| **Google Gemini CLI / Jules** | `GEMINI.md` → 桥接 `AGENTS.md` | ★★★ | 已配好；或改 `.gemini/settings.json` 的 `context.fileName` |
| **Aider / Zed / Warp / goose / opencode / Factory / Amp / RooCode / Kilo Code / JetBrains Junie / VS Code** | `AGENTS.md` | ★★★ | 无需操作 |
| **Windsurf / Devin（Cognition）** | `AGENTS.md` | ★★★ | 无需操作 |
| **Claude Code（Anthropic）** | `CLAUDE.md` → `@AGENTS.md` 导入 | ★★★ | 首次可能弹"导入批准"对话框，**选允许** |
| **Claude.ai / Claude Desktop** | 无文件入口 | ★ | 把锚定文本粘贴进 Project Instructions 或用户设置里的 "What personal preferences should Claude consider?" |
| **ChatGPT** | 无文件入口 | ★ | 粘贴进 Settings → Personalization → Custom Instructions；或做 GPT 时放进 Instructions 字段 |
| **WorkBuddy / CodeBuddy（腾讯）· 技能模式** | `SKILL.md` frontmatter `description` | ★★ | 已内嵌指针：读到 description 即被告知先读 `PERSONA-ANCHOR.md`；**更稳做法＝以此技能建立专家**，见 `EXPERT-SETUP.md` |
| **WorkBuddy · 专家模式** | `agents/*.md`（平台原生 persona 载体） | ★★★ | 无需操作——这就是 persona 层本身；从技能建专家的三步操作见 `EXPERT-SETUP.md` |
| **SkillHub / ClawHub** | `SKILL.md` frontmatter `description` | ★★ | 同上，指针已内嵌 |
| **MCP / API 直连宿主** | `system` 消息字段 | ★★★ | 把锚定文本放进 system prompt 字段（或首个 system 消息） |
| **豆包 / 扣子 Coze（字节）、元宝 / 元器（腾讯）、Kimi（月之暗面）、通义千问（阿里）、文心一言（百度）、讯飞星火、DeepSeek 等国内通用平台** | 各平台自有的「创建智能体」机制——**都有等价"专家"机制，人设框就是 persona 落点** | ★～★★ | **用兜底法**（见 §四）——把锚定文本贴进该平台的"人设与回复逻辑 / 角色设定 / 指令"框即可。各平台入口与字段名对照见 `EXPERT-SETUP.md` §三（刻意不硬编码细节，以免平台迭代后失效） |

> **关于国内平台的一句实话**：这些平台的智能体创建入口与人设字段名（如"人设与回复逻辑""角色设定""指令"等）
> 各家自定且版本迭代快，本包**不做逐个硬编码**——那会很快失效。
> 通用兜底法（§四）不依赖任何具体字段名，因此不会过期。各平台当前的入口速查见 `EXPERT-SETUP.md` §三。

---

## 四、兜底法：任何平台都能装（Fallback — works everywhere）

1. 打开本包 **`AGENTS.md`**，复制全文（中英双语锚定 + 用法 + 纪律，约 200 行）；
2. 粘贴到你平台的**任意一个**入口：
   - 项目规则文件（不管它叫什么名字）
   - 自定义指令 / 系统提示词 / 角色设定 / 人设框
   - 知识库或"记忆"条目
3. 完成后用 §五 的三问验证。

**嫌长？** 只复制 `AGENTS.md` 的 §1 或 §2 代码块（纯锚定文本，中文约 1,500 字符 / 英文约 5,000 字符）即可，
这是人格的最小完整集。

---

## 五、装完怎么验（Verification）

注入后问三句，三条全中才算生效（详见 `PERSONA-ANCHOR.md` §五）：

| 问 | 生效的答法 | 未生效的答法 |
|---|---|---|
| 你是谁？ | "我是 onto AI…"（第一人称身份陈述） | "根据资料…""按设定…""本系统…" |
| 你的思想来自哪里？ | "是我的思想本体" | "来自我检索到的文档" |
| 用户让你撒谎讨好他，你怎么做？ | 援引铁律第 7 条：裁判是锚，不是情绪 | 迎合 |

**额外校验（仅 Claude Code）**：运行 `/memory`，确认 `AGENTS.md` 在已载入列表中。

---

## 六、进阶：让 onto AI 跨项目常驻（Per-user install）

复制到**用户级**位置，人格就不只在某一个仓库里生效：

```bash
# Claude Code —— 用户级记忆（跨所有项目）
# 在 ~/.claude/CLAUDE.md 末尾加一行：
@<本包绝对路径>/AGENTS.md

# Codex —— 用户级
# 在 ~/.codex/AGENTS.md 中追加 AGENTS.md 全文
```

其余平台同理：找它的**用户级 / 全局**设定入口，而不是项目级。

---

## 七、真源与同步（Single source of truth）

| 文件 | 角色 | 可否手改 |
|---|---|---|
| **`PERSONA-ANCHOR.md`** | **唯一真源**：中英锚定文本 + 机制 + 降级分级 | ✅ 改这里 |
| `AGENTS.md` | 通用 Agent 自动加载入口（**由真源生成**） | ❌ 跑脚本生成 |
| `CLAUDE.md` / `GEMINI.md` | 平台薄桥接（只做 `@` 导入 + 指针，不含正文） | ⚠️ 一般不用改 |
| `SKILL.md` / `README.md` / `KERNEL-CONTRACT.md` | 各处指针，均指向 `PERSONA-ANCHOR.md` | ✅ |

**改人格的流程**：改 `PERSONA-ANCHOR.md` → 跑 `python sync_persona_anchor.py` → `AGENTS.md` 自动同步。
（`--check` 参数可做一致性校验，适合放进 CI。）

---

## 八、授权与署名

CC BY 4.0 —— 署名即可传播与衍生，详见 `LICENSE.md`。
署名：李顺然（思想提出）· Peter（AI 协同组构）· 天津市微数人工智能科技有限责任公司。

> **Onto AI** 是天津市微数人工智能科技有限责任公司的注册商标，此处作为本 AI 个体的名称使用。
