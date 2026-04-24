---
name: assignment-feedback
description: Generates instructor feedback reports for student draft manuscripts in the assignments/ folder, grounded in FISH 290 course material from lectures/ and readings/. Assigns a sequential two-digit prefix to the draft and writes a matching plain-text feedback file into feedback/. Use when the instructor asks to assess, review, evaluate, give feedback on, or critique a draft or partial draft manuscript located in assignments/.
---

# Assignment Feedback

This skill produces a plain-text feedback report for a student draft (full or partial manuscript) in `assignments/`. It enforces a global two-digit numbering scheme that keeps the draft filename and the feedback filename in lockstep, and grounds every suggestion in specific files from `lectures/` and `readings/`.

## When to use

Invoke automatically when the instructor says things like:

- "Assess `assignments/<file>`"
- "Give feedback on this draft"
- "Review this partial manuscript"
- "Evaluate / critique this student submission"

If no file is specified, ask which file in `assignments/` to assess.

## Workflow

Track progress with this checklist:

```
- [ ] 1. Identify the target draft in assignments/
- [ ] 2. Compute the next two-digit prefix NN
- [ ] 3. Rename the draft to NN_<original name>
- [ ] 4. Read the draft content
- [ ] 5. Consult rubric.md and relevant lectures/ + readings/
- [ ] 6. Write feedback to feedback/NN_<stem>_feedback.txt
- [ ] 7. Report the two resulting paths to the instructor
```

### 1. Identify the draft

List `assignments/` if needed. The target is typically the only unprefixed file, or the one the instructor names explicitly.

### 2. Compute the next prefix (global sequential)

Scan BOTH `assignments/` and `feedback/` for any filenames beginning with `NN_` (two digits + underscore):

```bash
rg -o '(^|/)\d{2}_' assignments feedback --no-filename 2>/dev/null | grep -oE '^\d{2}' | sort -u | tail -1
```

Or via `ls`:

```bash
ls assignments feedback 2>/dev/null | grep -oE '^[0-9]{2}_' | sort -u | tail -1
```

- If no prefix found, next = `01`.
- Otherwise, next = highest + 1, zero-padded to two digits (`01`..`99`).

### 3. Rename the draft

Use `git mv` when the file is tracked, otherwise `mv`:

```bash
git mv "assignments/<original>" "assignments/NN_<original>" 2>/dev/null || mv "assignments/<original>" "assignments/NN_<original>"
```

Do not rename a file that already starts with `NN_`; reuse its existing prefix.

### 4. Read the draft

Use the Read tool on the renamed file. Supported: `.md`, `.txt`, `.pdf`, `.docx`. If the draft is a format that cannot be read, inform the instructor and stop.

### 5. Consult sources

Read `rubric.md` (alongside this SKILL.md) for the section-to-source mapping. Then open the most relevant `readings/` PDFs/docs for any section the draft contains. Cite each recommendation by filename.

`.pptx` and `.key` lecture files cannot be parsed directly. Reference them by filename only when the week/topic is clearly relevant (e.g., `lectures/290 Week 3.pptx` for methods).

### 6. Write the feedback file

Write to `feedback/NN_<stem>_feedback.txt` where `<stem>` is the original filename without extension.

Use this exact plain-text template (no Markdown formatting characters beyond the headers shown):

```
FEEDBACK: <original filename with extension>
Assignment #: NN
Date: YYYY-MM-DD

SUMMARY
<2-3 sentence overall assessment of the draft>

STRENGTHS
- <specific strength>
- <specific strength>

AREAS FOR IMPROVEMENT

Introduction
  - <comment>  [ref: readings/The Introduction Section.pdf]

Methods
  - <comment>  [ref: readings/The Methods Section.pdf; readings/Methods to edit 2023.docx]

Results
  - <comment>  [ref: readings/The Results Section.pdf]

Discussion
  - <comment>  [ref: readings/The Discussion Section.pdf; readings/Dealing with Limitations.pdf]

Writing & Brevity
  - <comment>  [ref: readings/Brevity.pdf; readings/Scientific Writing as Storytelling.pdf]

SPECIFIC LINE-LEVEL SUGGESTIONS
- p.X / section Y: "<short quote or paraphrase>" -> <concrete suggestion>

RECOMMENDED NEXT STEPS
1. <actionable step>
2. <actionable step>
3. <actionable step>

SOURCES CONSULTED
- readings/<file>
- readings/<file>
- lectures/<file>
```

Rules for the template:

- Omit any section of "AREAS FOR IMPROVEMENT" that does not apply to the draft (e.g., a partial manuscript missing a Discussion).
- Every bullet under "AREAS FOR IMPROVEMENT" must end with a `[ref: ...]` tag pointing to one or more real files in `readings/` or `lectures/`.
- Keep feedback concrete, kind, and actionable. Avoid generic praise.
- Use plain ASCII hyphens for bullets; do not use Markdown syntax.

### 7. Report to the instructor

Reply with a short confirmation that lists:

- The renamed draft path
- The feedback file path
- A one-sentence summary of the overall assessment

## Examples

Instructor: "Assess `assignments/Mantegna_ch1.docx`"

Agent actions (no prefixes yet in repo):

1. Scan -> no existing prefix, so `NN = 01`.
2. `git mv assignments/Mantegna_ch1.docx assignments/01_Mantegna_ch1.docx`
3. Read `assignments/01_Mantegna_ch1.docx`.
4. Consult `rubric.md`, then relevant readings (e.g., `readings/The Introduction Section.pdf`, `readings/Ten Simple Rules Structuring Papers.pdf`).
5. Write `feedback/01_Mantegna_ch1_feedback.txt` using the template.
6. Report both paths.

## Additional resources

- Section-to-source mapping: [rubric.md](rubric.md)
