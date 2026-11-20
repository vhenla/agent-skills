# agent-skills

个人跨 Agent 通用 Skills 仓库，兼容 Claude Code、Codex、Cursor、Antigravity、Gemini CLI 等。

## Skills 列表

- **`skills/phonetic-cards`**：将任意英文单词/人名列表生成单文件、离线可用、一点就读的音标卡片页面：
  - 支持美音/英音 IPA 与朗读同步切换、0.75x 慢速、顺序全部连读；
  - 兼容桌面端及 iOS Safari / Obsidian / Alook；
  - 支持可选**自动推送到云端**（由 Agent 询问你偏好的方式，如自有服务器 SSH/SCP、GitHub Pages 等，配置仅存于本机 `~/.config/phonetic-cards/deploy.json`，零隐私硬编码）并自动生成可直接粘贴的 Obsidian 单行 `<iframe>` 嵌入代码。

## 一句话让任意 Agent 安装

把下面这句话发给任何支持终端的 Agent 即可：

```text
请帮我从 https://github.com/vhenla/agent-skills 的 skills/phonetic-cards 安装 phonetic-cards 到你自己的全局 skills 目录里。
```

或使用 CLI 一键安装：

```bash
npx skills add vhenla/agent-skills --skill phonetic-cards
```
