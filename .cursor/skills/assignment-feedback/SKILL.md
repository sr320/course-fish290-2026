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
- [ ] 1.  Identify the target draft in assignments/
- [ ] 2.  Compute the next two-digit prefix NN
- [ ] 3.  Rename the draft to NN_<original name>
- [ ] 4.  Read the draft content
- [ ] 5a. List every rubric.md file mapped to every section present in the draft
- [ ] 5b. Read each listed .pdf / .docx in full (Read tool); do not skim
- [ ] 5c. Write a 1-2 sentence internal note on each file's key emphasis
- [ ] 5d. Verify every planned feedback bullet ties to a specific point from a note
- [ ] 6.  Write feedback to feedback/NN_<stem>_feedback.txt
- [ ] 6.5 Verify citation grounding (see "Verify citation grounding" below)
- [ ] 7.  Report the two resulting paths to the instructor
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

### 5. Read the mapped sources

Read `rubric.md` (alongside this SKILL.md) for the section-to-source mapping. Then, for every section present in the draft (Introduction, Methods, Results, Discussion, Abstract, etc.):

1. List every file listed for that section in `rubric.md`.
2. Read each `.pdf` and `.docx` in that list with the Read tool. Do not rely on filenames, prior knowledge, or memory from earlier sessions. "Consult" means "open and read."
3. After reading each file, write a brief internal note (1-2 sentences) capturing its key emphasis (e.g., "Week 7a: Discussion follows a 5-paragraph template; limitations should not be the last paragraph; 'But, yes' framing is preferred over 'Yes, but'.").
4. Every bullet you eventually write under "AREAS FOR IMPROVEMENT" must reflect a specific point from one of those notes, not just name-drop a filename.

Re-read on rubric change. If `rubric.md` has been edited since a previous feedback file was written, treat the source list as untrusted: read every rubric-mapped file for sections in the draft from scratch in this session, even if you read older versions previously. Do not assume prior knowledge transfers. Swapping `[ref: ...]` tags without re-grounding the substance is a defect (see "Anti-patterns" below).

Lecture file formats. PDF versions of lectures are readable; `.pptx` and `.key` are not. Always read and cite the `.pdf` version. If only the `.pptx`/`.key` exists for a lecture the rubric maps to, do not cite that lecture; pick another rubric-mapped source instead.

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
  - <comment>  [ref: readings/The Introduction Section.pdf; lectures/290 Week 2a.pdf]

Methods
  - <comment>  [ref: readings/The Methods Section.pdf; lectures/290 Week 4a.pdf]

Results
  - <comment>  [ref: readings/The Results Section.pdf; lectures/290 Week 5b 2025.pdf]

Discussion
  - <comment>  [ref: readings/The Discussion Section.pdf; readings/Dealing with Limitations.pdf; lectures/290 Week 7a 2025.pdf]

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
- Every bullet under "AREAS FOR IMPROVEMENT" must end with a `[ref: ...]` tag pointing to one or more real files in `readings/` or `lectures/`. Every file named in a `[ref: ...]` tag must have been read with the Read tool in the current session (see step 5).
- Each section of "AREAS FOR IMPROVEMENT" should cite at least one reading and at least one lecture PDF from the rubric for that section, when both are available.
- The `SOURCES CONSULTED` list must be a strict superset of all files appearing in `[ref: ...]` tags, and must include every file `rubric.md` maps to for sections present in the draft.
- Keep feedback concrete, kind, and actionable. Avoid generic praise.
- Use plain ASCII hyphens for bullets; do not use Markdown syntax.

### 6.5 Verify citation grounding

Before reporting to the instructor, confirm all of the following. If any check fails, fix the feedback file and re-check.

- Every file in `rubric.md` for the sections present in the draft appears in `SOURCES CONSULTED`.
- Every inline `[ref: ...]` tag names a file that was actually opened with the Read tool in this session.
- No bullet exists that names a rubric file but draws no substantive point from it (no name-drops).
- For every section in "AREAS FOR IMPROVEMENT," at least one bullet reflects a specific point from a lecture PDF mapped to that section in `rubric.md` (not just a reading).

### 7. Report to the instructor

Reply with a short confirmation that lists:

- The renamed draft path
- The feedback file path
- A one-sentence summary of the overall assessment

## Anti-patterns

These are defects, not stylistic preferences. Avoid all of them.

- Citation-only updates. Swapping `[ref: ...]` filenames to match a new `rubric.md` without re-reading the new files and revising the bullet content to reflect their specific emphases. If the rubric changes the lecture mapped to a section, the substance of that section's feedback must change to reflect what the new lecture actually emphasizes.
- Name-dropping. Adding a `[ref: <file>]` tag to a bullet whose content does not draw on a specific point from that file. Citations are evidence the file informed the bullet, not decoration.
- Skipping lectures. Reading only the `readings/` PDFs and citing lectures by filename without opening them. Lecture PDFs are readable and must be read whenever the rubric maps them to a section in the draft.
- Memory reuse across rubric edits. Reusing notes or phrasing from a previous feedback file because "the same lecture was cited last time" -- the rubric may have changed, lectures may have been re-titled, and prior session memory cannot be relied upon. Re-read from scratch.

## Examples

Instructor: "Assess `assignments/Mantegna_ch1.docx`"

Agent actions (no prefixes yet in repo):

1. Scan -> no existing prefix, so `NN = 01`.
2. `git mv assignments/Mantegna_ch1.docx assignments/01_Mantegna_ch1.docx`.
3. Read `assignments/01_Mantegna_ch1.docx`.
4. Open `rubric.md`. For each section present in the draft, list every rubric-mapped file, then read each `.pdf` / `.docx` with the Read tool. Take a 1-2 sentence note on each.
5. Draft `feedback/01_Mantegna_ch1_feedback.txt` using the template; ensure every bullet draws on a specific point from a note.
6. Run the citation-grounding verification (step 6.5).
7. Report both paths and a one-sentence summary.

## Additional resources

- Section-to-source mapping: [rubric.md](rubric.md)
