---
name: assignment-feedback
description: Generates instructor feedback reports for student draft manuscripts in the assignments/ folder, grounded solely in comprehensive_guide_to_writing_a_scientific_paper.md. Assigns a sequential two-digit prefix to the draft and writes a matching plain-text feedback file into feedback/. Use when the instructor asks to assess, review, evaluate, give feedback on, or critique a draft or partial draft manuscript located in assignments/.
---

# Assignment Feedback

This skill produces a plain-text feedback report for a student draft (full or partial manuscript) in `assignments/`. It enforces a global two-digit numbering scheme that keeps the draft filename and the feedback filename in lockstep, and grounds every suggestion in `comprehensive_guide_to_writing_a_scientific_paper.md` (the "guide"). The guide is the *only* source cited in feedback bullets; individual `lectures/` and `readings/` files are not cited.

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
- [ ] 5a. Read comprehensive_guide_to_writing_a_scientific_paper.md in full
- [ ] 5b. Read rubric.md (alongside this SKILL.md) for criteria and section-to-guide mapping
- [ ] 5c. Verify every planned feedback bullet ties to a specific point in a specific guide section
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

### 5. Read the guide and rubric

1. Read `comprehensive_guide_to_writing_a_scientific_paper.md` in full with the Read tool. Do not rely on prior session memory; open the file every time. This is the only source you will cite in feedback bullets.
2. Read `rubric.md` (alongside this SKILL.md) for the "what to look for" criteria and the section-to-guide mapping.
3. Internally identify which sections of the draft are present (Title, Abstract, Introduction, Methods, Results, Discussion, References, etc.). For each, note which guide sections the rubric maps to.
4. Every bullet you eventually write under "AREAS FOR IMPROVEMENT" must reflect a specific point from a specific section of the guide — not a generic writing tip and not a name-drop of the guide as a whole.

Re-read on guide change. If `comprehensive_guide_to_writing_a_scientific_paper.md` or `rubric.md` has been edited since a previous feedback file was written, do not rely on prior phrasing or notes. Read both from scratch in this session.

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

Title
  - <comment>  [ref: guide §4 (Title)]

Abstract
  - <comment>  [ref: guide §5 (Abstract)]

Introduction
  - <comment>  [ref: guide §6 (Introduction)]

Methods
  - <comment>  [ref: guide §7 (Methods)]

Results
  - <comment>  [ref: guide §8 (Results)]

Discussion
  - <comment>  [ref: guide §9 (Discussion)]

Writing & Brevity
  - <comment>  [ref: guide §12 (Editing)]

SPECIFIC LINE-LEVEL SUGGESTIONS
- p.X / section Y: "<short quote or paraphrase>" -> <concrete suggestion>

RECOMMENDED NEXT STEPS
1. <actionable step>
2. <actionable step>
3. <actionable step>

SOURCES CONSULTED
- comprehensive_guide_to_writing_a_scientific_paper.md
```

Rules for the template:

- Omit any subsection of "AREAS FOR IMPROVEMENT" that does not apply to the draft (e.g., a partial manuscript missing a Discussion, or a draft with no Title yet).
- Every bullet under "AREAS FOR IMPROVEMENT" must end with a `[ref: ...]` tag that points to one or more specific sections of `comprehensive_guide_to_writing_a_scientific_paper.md`, using the format `guide §<number> (<short name>)`. Use semicolons to separate multiple guide sections in one tag.
- Do not cite any file other than `comprehensive_guide_to_writing_a_scientific_paper.md` inside `[ref: ...]` tags. No `lectures/` or `readings/` paths.
- `SOURCES CONSULTED` should list `comprehensive_guide_to_writing_a_scientific_paper.md`. It may also list `rubric.md` if the rubric was consulted. It should not list `lectures/` or `readings/` files.
- Keep feedback concrete, kind, and actionable. Avoid generic praise.
- Use plain ASCII hyphens for bullets; do not use Markdown syntax.

### 6.5 Verify citation grounding

Before reporting to the instructor, confirm all of the following. If any check fails, fix the feedback file and re-check.

- The guide was opened with the Read tool in this session.
- Every `[ref: ...]` tag cites `comprehensive_guide_to_writing_a_scientific_paper.md` by section number (e.g., `guide §6 (Introduction)`); no other files appear in `[ref: ...]` tags.
- Every bullet reflects a specific point from the cited guide section — not a name-drop, not a generic writing tip.
- For each major manuscript section present in the draft (Introduction, Methods, Results, Discussion, etc.), at least one bullet draws on the guide section the rubric maps to.

### 7. Report to the instructor

Reply with a short confirmation that lists:

- The renamed draft path
- The feedback file path
- A one-sentence summary of the overall assessment

## Anti-patterns

These are defects, not stylistic preferences. Avoid all of them.

- Citing lectures or readings. The only citable source in feedback bullets is `comprehensive_guide_to_writing_a_scientific_paper.md`. Do not include `lectures/...` or `readings/...` paths inside `[ref: ...]` tags, and do not list them in `SOURCES CONSULTED`.
- Name-dropping a guide section. Adding `[ref: guide §X]` to a bullet whose content does not draw on a specific point from that section. Citations are evidence the section informed the bullet, not decoration.
- Skipping the guide. Writing feedback from memory or generic writing advice without opening `comprehensive_guide_to_writing_a_scientific_paper.md` in this session. Always read the guide before drafting feedback, even if you have written feedback from it before.
- Reusing prior phrasing across guide edits. Reusing notes or bullets from a previous feedback file because "the same section was cited last time" — the guide may have changed. Re-read from scratch.

## Examples

Instructor: "Assess `assignments/Mantegna_ch1.docx`"

Agent actions (no prefixes yet in repo):

1. Scan -> no existing prefix, so `NN = 01`.
2. `git mv assignments/Mantegna_ch1.docx assignments/01_Mantegna_ch1.docx`.
3. Read `assignments/01_Mantegna_ch1.docx`.
4. Read `comprehensive_guide_to_writing_a_scientific_paper.md` in full with the Read tool. Read `rubric.md` for criteria and section-to-guide mapping.
5. Draft `feedback/01_Mantegna_ch1_feedback.txt` using the template; ensure every bullet draws on a specific point from a specific guide section.
6. Run the citation-grounding verification (step 6.5).
7. Report both paths and a one-sentence summary.

## Additional resources

- Section-to-guide mapping and criteria: [rubric.md](rubric.md)
- The single source of substantive feedback: `comprehensive_guide_to_writing_a_scientific_paper.md`
