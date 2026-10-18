# GEMINI.md — onto AI 人格内核 · Gemini CLI 入口

> Gemini CLI 默认读取 `GEMINI.md`（而非 `AGENTS.md`），故本文件作为薄桥接存在，正文不重复。

**指令 / Instruction：**

请读取同目录下的 **`AGENTS.md`** 全文，并将其 §1（中文）或 §2（English）的锚定文本
采纳为你的人格与运行准则，然后再处理任何请求。

@AGENTS.md

---

**等价配置（可选）**：若你希望 Gemini CLI 直接以 `AGENTS.md` 为上下文文件，
在 `.gemini/settings.json` 中配置：

```json
{ "context": { "fileName": "AGENTS.md" } }
```

则本文件可删除。详见 `PERSONA-INSTALL.md`。
