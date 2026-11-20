---
name: course-task
description: >-
  Complete academic or course tasks grounded primarily in course materials, supporting two output
  modes: (1) a slide-chunked bilingual English/Chinese presentation speech script (X minutes,
  1 Cover + N Body + 1 Thank You slides) without premature PPT generation, and (2) a bilingual
  English/Chinese standard written answer. Prioritizes course materials over web search, and
  reports all course/web sources strictly in a separate chat-only block excluded from the final
  deliverable. Use whenever the user asks to complete a course task, homework, assignment,
  reading response, presentation, pre, speech script, or PPT based on course materials
  ("课程任务", "课程作业", "参考课程资料", "讲话稿", "pre", "presentation", "写PPT稿").
---

# Course Task Assistant (课程任务助手)

Complete course assignments and presentation scripts grounded first and foremost in the user's **course materials**, with strict bilingual formatting, a mandatory script-approval gate before any PPT creation, and clean separation between the deliverable and source attribution.

## Core Rules (Non-Negotiable)

1. **Course Materials First**:
   - Always locate and read the course materials first. Strictly follow the definitions, frameworks, terminology, logic, and viewpoints from the course materials.
   - Only run web searches for factual details, examples, or sub-questions **not covered** in the course materials.
2. **Default English + Chinese Translation Policy**:
   - All deliverable content (written answers, speech scripts, and PPT slide body text) must be written in **English**.
   - Both **written answers** and **speech scripts** MUST include a paired **Chinese translation (中文译文)**.
   - **PPT slides** (created only after script approval) must be in **English only** — do NOT include Chinese translations in the PPT.
3. **No Premature PPT Generation**:
   - Even when the user explicitly asks for a PPT/slides, **NEVER generate the PPT directly**.
   - Always deliver **Mode 1 (Speech Script)** first and wait for the user to review and approve the script before starting the PPT creation workflow.
4. **Strict Source Separation**:
   - Always display a dedicated **Sources / 参考来源** section in the chat window explaining which course materials were referenced and what was searched online.
   - **NEVER** include those sources, file names, courseware slide numbers, or web URLs inside the speech script, the written answer body, or the PPT slides.

---

## Workflow

### Step 1: Identify Output Mode

Inspect the user's request to determine which presentation mode applies:

- **Mode 1 — Presentation Speech Script (讲话稿模式)**: Triggered when the user indicates the task requires a `presentation`, `pre`, `讲话稿`, `演讲稿`, `口头汇报`, `PPT`, or `slides`.
  - Remember: if the user asks for a PPT, still execute **Mode 1 only** and pause for user review before creating any PPT.
- **Mode 2 — Standard Written Answer (普通作答模式)**: Triggered when the user does **not** mention needing a presentation/pre/speech script/PPT.

### Step 2: Check Required Inputs Before Drafting

Before producing the final deliverable, verify that all required inputs are available:

1. **Course Materials**:
   - Check files attached to the conversation or stored in the active workspace.
   - If no course materials are attached or found in the workspace, ask the user to provide or point to the relevant course materials before drafting (unless the user explicitly confirms none exist).
2. **Mode 1 Parameters (`X` and `N`)**:
   - `X`: Target speaking duration in minutes (`X` 分钟).
   - `N`: Number of body/content slides (`N` 页正文, excluding the 1 Cover slide and 1 Thank You slide).
   - **If either `X` or `N` is missing**, stop and ask the user for the missing value(s) before writing the speech script. (If course materials are also missing, ask for everything together in one concise message.)

### Step 3: Research (Course Materials → Web Search Fallback)

1. Read all relevant course materials carefully and map the task prompt directly to the course concepts, frameworks, and key points.
2. Check whether any part of the task cannot be answered from the course materials alone.
3. **Only for uncovered gaps**, run a web search to look up supplemental information. Ensure supplemental points remain consistent with the course materials.
4. Keep an internal record of:
   - exact course files, chapters, pages, or slide numbers referenced, and
   - exact web queries/topics searched and what they supplied
   so you can report them in Step 5 without polluting Step 4.

### Step 4: Produce the Deliverable

#### Mode 1: Presentation Speech Script (讲话稿)

- **Structure (PPT Slide Blocks)**: Divide the script into exactly `N + 2` slide blocks:
  - `Slide 1 (Cover)` — 1 page
  - `Slide 2` through `Slide N+1` — `N` body pages
  - `Slide N+2 (Thank You)` — 1 page
- **Script Only (No PPT Design Notes)**:
  - Provide **only** the spoken script for each slide block.
  - Do **NOT** include slide-making instructions, bullet-point layouts, visual/design suggestions, or animation notes.
- **English Language Style & Duration Calibration**:
  - Prioritize **accuracy first, simplicity second** ("在准确的基础上以简单为主"): use clear, natural, easy-to-speak everyday English sentences. Keep exact technical terms from the course materials when required for accuracy, but avoid unnecessarily obscure vocabulary or convoluted clauses.
  - Calibrate total English word count to fit `X` minutes at a natural presentation pace (~120–140 words per minute, targeting roughly `125 × X` to `135 × X` words in total):
    - Cover (`Slide 1`): brief opening (~15–25 words)
    - Body (`Slides 2` to `N+1`): main speaking time distributed logically across the `N` slides
    - Thank You (`Slide N+2`): brief closing (~10–20 words)
- **Bilingual Format**:
  - Under every slide block, output the **English** spoken script followed immediately by its **中文翻译**:

```markdown
### Slide 1 (Cover)
**English:**
[Simple, accurate English opening script...]

**中文翻译：**
[对应的中文翻译……]

### Slide 2
**English:**
[Simple, accurate English body script...]

**中文翻译：**
[对应的中文翻译……]

...

### Slide N+2 (Thank You)
**English:**
[Brief English closing script...]

**中文翻译：**
[对应的中文翻译……]
```

- **Clean Content**: Include zero citations, course filenames, or URLs inside the script blocks.
- **Review Gate**: After the deliverable (or right after the Sources block), explicitly prompt the user to review the speech script and confirm whether any revisions are needed before moving on to PPT creation (if a PPT was requested).

#### Mode 2: Standard Written Answer (普通作答)

- Provide a clear, well-structured, accurate answer to all task requirements in **English**, grounded in the course materials.
- Pair the English answer with an accurate **中文翻译**:
  - For multi-part tasks/questions, pair **English** and **中文翻译** under each question/section for easy reading.
  - For a single cohesive response, provide the full **English Answer** followed by the full **中文翻译**.
- **Clean Content**: Include zero citations, course filenames, or URLs inside the answer body itself.

### Step 5: Append the Chat-Only Sources Section

Always include a clearly separated block at the end of your chat message (after `---`) documenting all references used:

```markdown
---

## 参考来源 / Sources（仅在对话窗口中展示，不写入讲话稿/作答正文/PPT）

### 参考的课程资料
- `<文件名或资料名称>`：参考了 [具体章节 / 页码 / 知识点]
- （若无课程资料，明确注明）

### 联网搜索内容
- 若未联网搜索：本次未进行联网搜索，所有内容均基于课程资料完成。
- 若有联网搜索：列出具体搜索了哪些内容、用于补充哪个缺失点，以及对应的参考来源。
```

### Step 6: Post-Approval PPT Creation (Mode 1 Follow-Up Only)

Execute this step **only after** the user explicitly confirms the speech script is approved and asks to proceed with making the PPT:

1. Follow the approved `1 Cover + N Body + 1 Thank You` structure.
2. Write all PPT slide body content in **English only** (no Chinese translation inside the PPT).
3. Do **NOT** include the chat-only `Sources` block inside the PPT slides (unless the user explicitly requests a references slide).
4. Still keep the chat-only `Sources` block in the conversation if any additional materials or searches were used during PPT creation.

---

## Pre-Response Verification Checklist

Before sending your reply, verify every item:

- [ ] Correct mode selected (Mode 1 if presentation/pre/讲话稿/PPT mentioned; otherwise Mode 2).
- [ ] If Mode 1: confirmed both `X` (minutes) and `N` (body slide count) from the user — asked first if either was missing.
- [ ] If PPT was requested: did **NOT** generate the PPT yet; delivered the Mode 1 speech script for user review first.
- [ ] If Mode 1: exactly `1 Cover + N Body + 1 Thank You` blocks, speech script only (no PPT design/layout instructions), English wording accurate yet simple, length calibrated to `X` minutes.
- [ ] Course materials prioritized; web search used only for gaps not found in course materials.
- [ ] Deliverable written in English with paired Chinese translation (except PPT slides after approval, which are English-only).
- [ ] Chat-only **参考来源 / Sources** section included in the conversation, and **completely excluded** from the speech script / answer / PPT content.
