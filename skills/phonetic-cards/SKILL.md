---
name: phonetic-cards
description: >-
  Generate a self-contained, offline-ready, click-to-pronounce English phonetic transcription (IPA) card list as a single HTML file using the browser's native Web Speech API. Use this skill whenever the user provides English words, phrases, or names and asks for a phonetic list, IPA chart with clickable pronunciation, or click-to-read vocabulary cards ("音标清单", "点击发音", "一点就读", "单词音标卡片").
---

# Phonetic Cards (Portable Cross-Agent Skill)

Generate a clean, single-file, zero-dependency HTML page that displays English words, phrases, or names with US/UK IPA transcriptions, concise Chinese glosses, and one-click pronunciation powered by `window.speechSynthesis`.

## Workflow

1. **Extract and verify entries**:
   - Keep every word, phrase, or proper name provided by the user in the exact original order.
   - Build a JSON array where each item has:
     - `word`: exact English word, phrase, or proper name
     - `us`: standard General American IPA wrapped in slashes (e.g. `"/kwoʊt/"`)
     - `uk`: standard Received Pronunciation (UK) IPA wrapped in slashes (e.g. `"/kwəʊt/"`)
     - `zh`: concise Chinese meaning (2–4 characters, e.g. `"宗教"`, `"脆弱的"`, `"尼采"`)
   - For non-English proper names (e.g. German or French names), put the standard English pronunciation in `us`/`uk` and mention the original-language IPA briefly in your reply.

2. **Populate the HTML template**:
   - Read [resources/template.html](./resources/template.html).
   - Replace the `__WORDS_JSON__` placeholder with the JSON array from Step 1.

3. **Deliver the HTML file**:
   - **Coding / CLI agents** (Claude Code, Codex, Cursor, Gemini CLI, etc.): write the populated HTML to `./phonetic-list.html` (or the path requested by the user) and provide the file path so the user can open it directly in any browser.
   - **UI agents with HTML preview / artifacts**: render the populated HTML directly in the preview/artifact pane (or save and embed it per host conventions).

4. **Keep the response concise**:
   - State where the file was saved (or that the interactive widget is ready), and briefly mention the built-in controls (US/UK toggle, 0.75x slow speed, Play All).
   - Add 1–2 short pronunciation notes only if there are notable US/UK differences or non-English proper names.
   - Do **not** repeat the entire vocabulary list as a static Markdown table unless the user explicitly asks for one.

## Requirements

- Keep the output strictly single-file and offline-ready: no external CSS/JS CDNs and no third-party audio URLs.
- Preserve the Web Speech API reliability safeguards in `resources/template.html` (`onvoiceschanged`, `cancel()` + short delay before `speak()`, and retaining the active `SpeechSynthesisUtterance` reference).
