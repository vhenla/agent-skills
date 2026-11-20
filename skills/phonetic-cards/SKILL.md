---
name: phonetic-cards
description: >-
  Generate a self-contained, offline-ready, click-to-pronounce English phonetic transcription (IPA) card list as a single HTML file using the browser's native Web Speech API, with optional cloud publishing (SSH server, GitHub Pages, etc.) and Obsidian iframe embedding. Use this skill whenever the user provides English words, phrases, or names and asks for a phonetic list, IPA chart with clickable pronunciation, or click-to-read vocabulary cards ("音标清单", "点击发音", "一点就读", "单词音标卡片").
---

# Phonetic Cards (Portable Cross-Agent Skill)

Generate a clean, single-file, zero-dependency HTML page that displays English words, phrases, or names with US/UK IPA transcriptions, concise Chinese glosses, and one-click pronunciation powered by `window.speechSynthesis`. Optionally publish the generated HTML to the user's preferred cloud host and output a ready-to-paste Obsidian `<iframe>` snippet.

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
   - Pick a collision-proof, URL-safe filename using `YYYYMMDD-HHMM-<topic-slug>.html` (e.g. `20261120-2038-religion-ethics.html`), unless the user explicitly specifies a filename. Before saving or uploading, if a file with the same name already exists at the destination, append a numeric or short random suffix so existing cards are never overwritten.

3. **Deliver locally or in preview**:
   - **Coding / CLI agents** (Claude Code, Codex, Cursor, Gemini CLI, etc.): save the populated HTML locally (e.g. `./cards/<filename>` or `./phonetic-list.html`, or the path requested by the user).
   - **UI agents with HTML preview / artifacts**: render the populated HTML in the preview/artifact pane (or save and embed it per host conventions).

4. **Optional Cloud Publishing & Obsidian Embed**:
   - Check if a machine-local deploy config exists at `~/.config/phonetic-cards/deploy.json` (this file is strictly local and MUST NEVER be committed to any public repository).
   - **If `deploy.json` exists** (and the user did not ask for local-only): offer or proceed according to its configured `auto_publish` setting (`true` = upload automatically after generating; `false` = ask before uploading).
   - **If no `deploy.json` exists AND the user asks to publish to the cloud or embed in Obsidian**:
     - Ask the user which publishing method they want to use:
       1. **Own server via SSH/SCP** — ask for SSH host alias, remote directory path, whether `sudo` is needed for moving/chmod, and the public HTTPS base URL.
       2. **GitHub Pages** — ask for target GitHub repo (e.g. `<owner>/<repo>`) and subfolder path, push via git/`gh`, and construct the `https://<owner>.github.io/<repo>/...` URL.
       3. **Custom / other hosting** (e.g. Cloudflare Pages, S3/R2/OSS CLI, or manual upload) — follow the user's preferred upload command or destination.
       4. **Local only** — skip cloud upload.
     - After the user chooses, offer to save their publishing settings locally to `~/.config/phonetic-cards/deploy.json` so future runs do not need to ask again.
   - **If no `deploy.json` exists AND the user did not mention cloud/Obsidian**: do not block the response with questions; simply add a one-line note at the end offering cloud publishing / Obsidian embedding if needed.
   - **After uploading to cloud**:
     - Verify the live URL responds with HTTP 200 and `text/html`, and warn the user if `X-Frame-Options` (`DENY`/`SAMEORIGIN`) or restrictive `frame-ancestors` headers would block embedding.
     - Provide the live HTTPS link and a **single-line** Obsidian `<iframe>` snippet (critical: Obsidian's Markdown parser breaks multi-line `<iframe>` tags!):
       ```html
       <iframe src="https://example.com/cards/your-list.html" width="100%" height="540" style="border: 0; border-radius: 10px;"></iframe>
       ```

5. **Keep the response concise**:
   - State where the file was saved (and the live URL + single-line Obsidian `<iframe>` if published).
   - Add 1–2 short pronunciation notes only if there are notable US/UK differences or non-English proper names.
   - Do **not** repeat the entire vocabulary list as a static Markdown table unless the user explicitly asks for one.

## Local Deploy Config Schema (Optional, Local-Only)

Never hardcode personal servers, domains, or credentials inside `SKILL.md` or any shared repo. If the user opts to save preferences locally at `~/.config/phonetic-cards/deploy.json`, use this structure:

```json
{
  "method": "ssh",
  "auto_publish": true,
  "ssh_host": "my-server-alias",
  "remote_dir": "/var/www/site/cards",
  "use_sudo": false,
  "public_base_url": "https://example.com/cards"
}
```

Or for GitHub Pages:

```json
{
  "method": "github-pages",
  "auto_publish": true,
  "repo": "username/vocab-cards",
  "local_repo_dir": "~/vocab-cards",
  "subdir": "cards",
  "public_base_url": "https://username.github.io/vocab-cards/cards"
}
```

## Requirements

- Keep the generated HTML strictly single-file and offline-ready: no external CSS/JS CDNs and no third-party audio URLs.
- Preserve the Web Speech API reliability safeguards in `resources/template.html` (`onvoiceschanged`, calling `speechSynthesis.speak()` synchronously inside the user click handler for iOS Safari / WKWebView compatibility, preferring local voices, and retaining active `SpeechSynthesisUtterance` references).
- Never commit personal hostnames, private domains, tokens, or `~/.config/phonetic-cards/deploy.json` into any Git repository.
