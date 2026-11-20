# agent-skills

个人跨 Agent 通用 Skills 仓库，兼容 Claude Code、Codex、Cursor、Antigravity、Gemini CLI 等。

## Skills 列表

- **`skills/phonetic-cards`**：将任意英文单词/人名列表生成单文件、离线可用、一点就读的音标卡片页面：
  - 支持美音/英音 IPA 与朗读同步切换、0.75x 慢速、顺序全部连读；
  - 兼容桌面端及 iOS Safari / Obsidian / Alook；
  - 默认按 `YYYYMMDD-HHMM-<topic-slug>.html` 命名防重名；
  - 支持可选**自动推送到云端**（由 Agent 询问你偏好的方式，如自有服务器 SSH/SCP、GitHub Pages 等，配置仅存于本机 `~/.config/phonetic-cards/deploy.json`，零隐私硬编码）并自动生成可直接粘贴的 Obsidian 单行 `<iframe>` 嵌入代码。

- **`skills/bsu-ppt-master`**：将讲话稿、发言稿或课程资料（支持 `.md`、`.docx` 或纯文本）基于白俄罗斯国立大学（BSU）官方原生模板生成可编辑 PPTX：
  - **自包含内置模板**：内置 `resources/PPT模板3.pptx`（默认主模板，含 19 种原生版式、通用图标库及 BSU 各学院信息图）及 `PPT模板1.pptx`、`PPT模板2.pptx`（样式与素材补充），零本机路径依赖；
  - **固定制作约束**：强制走 `Edit Native PPTX` 在原生模板副本中编辑、保留母版与视觉体系、禁止出现 `Source` 标注、版式丰富美观并输出到当前项目 `exports/` 目录；
  - **前置依赖**：依赖 [**`ppt-master`**](https://github.com/hugohe3/ppt-master)（`npx skills add hugohe3/ppt-master`）。安装或首次使用时，Agent 会自动检查当前环境是否已安装 `ppt-master`——若本机其他 Agent 目录已有副本，会先询问是否软链接；若尚未安装，会先询问是否协助安装；
  - **可选环境能力**：如宿主环境提供 `office` / `office-pptx` 等专用 Office 技能（如 DSH）会按需配合调用；否则自动使用 `ppt-master` 自带脚本及本地 Python / LibreOffice 工具链完成读写与渲染检查。

- **`skills/course-task`**：基于课程资料完成各类学术/课程任务，自动区分「讲话稿」与「普通作答」两种模式：
  - **双模式自动切换**：提及 `presentation` / `pre` / 讲话稿 / PPT 时进入讲话稿模式，否则进入普通作答模式；即使明确需要 PPT，也强制先交付讲话稿，待审核无误后再进入 PPT 制作流程；
  - **讲话稿规范**：按 `封面 1 页 + 正文 N 页 + Thank You 1 页` 分块输出纯讲话稿（不夹杂 PPT 制作指导），英文用词在准确的基础上以简单为主，按 X 分钟校准字数（未提供 X 或 N 时先询问）；
  - **资料优先与双语规则**：严格优先遵循课程资料，仅对资料未覆盖部分联网搜索；默认英文作答并附中文译文（审核通过后制作的 PPT 正文仅保留英文）；
  - **来源严格隔离**：每次在对话窗口末尾单独列出参考的课程资料与联网搜索内容，绝不写入讲话稿、作答正文或 PPT 中。

## 安装 Skill

### 方式 1：一句话让任意 Agent 自动安装

把下面这句话发给任何支持终端的 Agent 即可（按需替换 skill 名称）：

```text
请帮我从 https://github.com/vhenla/agent-skills 的 skills/phonetic-cards 安装 phonetic-cards 到你自己的全局 skills 目录里。
```

```text
请帮我从 https://github.com/vhenla/agent-skills 的 skills/bsu-ppt-master 安装 bsu-ppt-master 到你自己的全局 skills 目录里。
```

```text
请帮我从 https://github.com/vhenla/agent-skills 的 skills/course-task 安装 course-task 到你自己的全局 skills 目录里。
```

### 方式 2：使用 CLI 一键安装

```bash
# 安装 phonetic-cards
npx skills add vhenla/agent-skills --skill phonetic-cards

# 安装 bsu-ppt-master（及其前置依赖 ppt-master）
npx skills add hugohe3/ppt-master
npx skills add vhenla/agent-skills --skill bsu-ppt-master

# 安装 course-task
npx skills add vhenla/agent-skills --skill course-task
```

## 更新 Skill

### 方式 1：一句话让 Agent 自动更新

直接对已安装对应 Skill 的 Agent 发送：

```text
请帮我从 https://github.com/vhenla/agent-skills 的 skills/phonetic-cards 更新你本地的 phonetic-cards skill 到最新版本。
```

```text
请帮我从 https://github.com/vhenla/agent-skills 的 skills/bsu-ppt-master 更新你本地的 bsu-ppt-master skill 到最新版本。
```

```text
请帮我从 https://github.com/vhenla/agent-skills 的 skills/course-task 更新你本地的 course-task skill 到最新版本。
```

### 方式 2：使用 CLI 更新

```bash
npx skills update
```

或重新执行安装命令覆盖到最新版：

```bash
npx skills add vhenla/agent-skills --skill phonetic-cards
npx skills add vhenla/agent-skills --skill bsu-ppt-master
npx skills add vhenla/agent-skills --skill course-task
```

> **提示**：更新 Skill 不会影响你本机私有配置文件 `~/.config/phonetic-cards/deploy.json`，云端推送配置无需重新填写，且本机多个 Agent 可自动共用同一份配置。
