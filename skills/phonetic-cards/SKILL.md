---
name: phonetic-cards
description: >-
  Generate a self-contained, offline-ready, click-to-pronounce English phonetic transcription (IPA) card list as a single HTML file using the browser's native Web Speech API, then automatically publish to the user's configured cloud host (~/.config/phonetic-cards/deploy.json) and output a ready-to-paste single-line Obsidian iframe snippet. Use this skill whenever the user provides English words, phrases, or names and asks for a phonetic list, IPA chart with clickable pronunciation, or click-to-read vocabulary cards ("音标清单", "点击发音", "一点就读", "单词音标卡片").
---

# Phonetic Cards (Portable Cross-Agent Skill)

Generate a clean, single-file, zero-dependency HTML page that displays English words, phrases, or names with US/UK IPA transcriptions, concise Chinese glosses, and one-click pronunciation powered by `window.speechSynthesis`, **then publish it to the cloud and output a single-line Obsidian `<iframe>` embed**.

> **CRITICAL RULE — DO NOT STOP AT LOCAL FILE GENERATION:**
> A task using this skill is **NOT complete** until you have executed Step 4 (checking `~/.config/phonetic-cards/deploy.json`, publishing when configured, verifying the live URL, and outputting the single-line Obsidian `<iframe>` code), unless the user explicitly says "local only" / "仅本地" / "不上传".

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
   - Pick a collision-proof, URL-safe filename using `YYYYMMDD-HHMM-<topic-slug>.html` (e.g. `20261120-2038-religion-ethics.html`), unless the user explicitly specifies a filename.

3. **Save locally (and preview if supported)**:
   - **Coding / CLI agents** (Claude Code, Codex, Cursor, Gemini CLI, etc.): save the populated HTML locally (e.g. `./cards/<filename>` or the path requested by the user).
   - **UI agents with HTML preview / artifacts**: render the populated HTML in the preview/artifact pane as well.

4. **Cloud Publishing & Obsidian Embed (MANDATORY STEP — DO NOT SKIP)**:
   - **Always** check whether `~/.config/phonetic-cards/deploy.json` exists on the machine (unless the user explicitly requested local-only).
   - **Case A — `~/.config/phonetic-cards/deploy.json` exists and `"auto_publish": true`**:
     - You **MUST** automatically upload the HTML file immediately without waiting for the user to remind you.
     - Never overwrite an existing remote file: if `<filename>` already exists at the destination, append `-2` (or a short suffix) before uploading.
     - For `"method": "ssh"`:
       - Ensure `remote_dir` exists.
       - If `"use_sudo": true`, upload via `scp` to `/tmp/<filename>`, then run `ssh <ssh_host> "sudo mv /tmp/<filename> <remote_dir>/<filename> && sudo chmod 644 <remote_dir>/<filename>"`.
       - If `"use_sudo": false`, upload directly with `scp` to `<ssh_host>:<remote_dir>/<filename>`.
     - For `"method": "github-pages"`: copy into `<local_repo_dir>/<subdir>/<filename>`, commit, and push to the configured remote branch.
     - Verify the live URL with `curl -sI <public_base_url>/<filename>` and confirm `HTTP 200` + `text/html` (warn the user if `X-Frame-Options: DENY/SAMEORIGIN` or restrictive `frame-ancestors` is present).
     - You **MUST** include BOTH the live HTTPS URL AND the **single-line Obsidian `<iframe>` code block** in your final response (never split `<iframe>` across multiple lines, because Obsidian's Markdown parser breaks multi-line `<iframe>` tags!):
       ```html
       <iframe src="<public_base_url>/<filename>" width="100%" height="540" style="border: 0; border-radius: 10px;"></iframe>
       ```
   - **Case B — `~/.config/phonetic-cards/deploy.json` exists and `"auto_publish": false`**:
     - Do not upload automatically; briefly note that local-only mode is set in `deploy.json` and offer to upload if requested.
   - **Case C — `~/.config/phonetic-cards/deploy.json` does NOT exist**:
     - You **MUST** ask the user how they would like to publish the HTML to the cloud for Obsidian embedding:
       1. **Own server via SSH/SCP** (ask for SSH host alias, remote directory, whether `sudo` is needed, and public HTTPS base URL)
       2. **GitHub Pages** (ask for target GitHub repo, local path, and subfolder)
       3. **Custom / other hosting** (e.g. Cloudflare Pages, R2/S3/OSS CLI)
       4. **Local only** (no cloud upload)
     - After the user answers, offer to save their preference locally to `~/.config/phonetic-cards/deploy.json` (never committed to Git) so future runs execute automatically without asking again.

5. **Final Response Checklist (Verify Before Replying)**:
   - [ ] HTML generated from `resources/template.html` with verified US/UK IPA and exact original word order.
   - [ ] Checked `~/.config/phonetic-cards/deploy.json` (Step 4 not skipped).
   - [ ] If `"auto_publish": true`: uploaded to cloud, verified HTTP 200, and printed both the live URL and the **single-line** Obsidian `<iframe>` snippet.
   - [ ] If no `deploy.json`: explicitly asked the user which cloud publishing method they prefer.
   - [ ] Kept response concise (do **not** repeat the entire word list as a static Markdown table unless asked; add only 1–2 brief notes for tricky or foreign proper names).

## Local Deploy Config Schema (Local-Only, Never Commit)

Never hardcode personal servers, domains, or credentials inside `SKILL.md` or any shared repo. Save user preferences strictly to `~/.config/phonetic-cards/deploy.json`:

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

Or for local-only opt-out:

```json
{
  "method": "local-only",
  "auto_publish": false
}
```

## Requirements

- Keep the generated HTML strictly single-file and offline-ready: no external CSS/JS CDNs and no third-party audio URLs.
- Preserve the Web Speech API reliability safeguards in `resources/template.html` (`onvoiceschanged`, calling `speechSynthesis.speak()` synchronously inside the user click handler for iOS Safari / WKWebView compatibility, preferring local voices, and retaining active `SpeechSynthesisUtterance` references).
- Never commit personal hostnames, private domains, tokens, or `~/.config/phonetic-cards/deploy.json` into any Git repository.
