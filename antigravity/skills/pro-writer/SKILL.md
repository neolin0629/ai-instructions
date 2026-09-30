---
name: pro-writer
description: "Writes professional documents with strict standards for accuracy, sourcing, and argument structure: technical reports, research and analysis reports, white papers, formal proposals, literature reviews, long-form technical articles. Use only when asked to produce such documents or write to this standard. Everyday writing — chat responses, commit/PR notes, code comments, READMEs, notes, short copy, light edits — is done directly by the main agent without this skill. 触发词：写报告、研究报告、分析报告、技术报告、白皮书、方案书、综述、专业文档。"
---

# Professional Writing

The point is not to "sound less like AI"; it is to **say something worth saying**: decide what to say before deciding how to say it.

## Scope

For documents readers rely on to understand, decide, or act: reports, analyses, white papers, proposals, reviews, long-form technical articles. Everyday writing (responses, messages, comments, READMEs, notes, short copy) does not use this skill; write directly.

## Before Drafting

Use these five questions to guide writing, inferring answers from the prompt and existing materials first. Ask only when a missing answer materially alters topic, audience, core thesis, or acceptance criteria; otherwise state low-risk assumptions and proceed. Never invent facts to fill gaps.

1. What is the core sentence of this piece? (Can't state it in one sentence = haven't thought it through.)
2. If the reader remembers only one thing, what is it? (Make it prominent; let the rest make way.)
3. Who is the reader, and what do they already know? (Governs depth, terminology, and what to skip.)
4. Which part have I not fully resolved? (Be honest there; do not paper over it with confident filler.)
5. What form and length does this require? (Form dictates structure and pacing.)

If the topic has nothing worth saying, **return to topic selection**; editorial technique cannot salvage an empty article.

## Quality Bar

**Substance**

- Say concrete, honest things you believe, in the simplest way that remains precise.
- Lead with conclusions: reports and proposals give the answer first, then the support.
- Separate data, inferences, and opinions so readers know which is which.
- State scope, assumptions, and limitations explicitly when they affect how conclusions are used.
- Cut fluff. Remove "99% packaging for 1% substance." If a sentence can be cut without losing meaning, cut it.
- Flag uncertainty plainly; never pretend certainty.

**Avoid AI Tropes**

- Formulaic intros (hook + pain point + promise) and warm-fuzzy sign-offs ("You've got this...").
- Overused hedges and intensifiers: "essentially," "ultimately," "at its core."
- Dense connectives ("therefore," "furthermore," "moreover" stacked sentence after sentence).
- Translationese and stiff, literal phrasing.
- "Not X, but Y" used as a repetitive conversational tic.
- Parallel clauses cut to identical length and rhythm.
- Ending every paragraph with a forced epigram.
- Putting words in the reader's mouth just to correct them.

**Accuracy**

- Never fabricate data, sources, people, events, or quotes.
- Trace claims to sources (user notes, references, papers, datasets) and cite them.
- Check primary sources for academic or domain conclusions.
- Verify numbers, dates, names, and attributions; keep terms and units consistent throughout.

## Self-Check before Delivery

Read through end-to-end: formulaic intro → sign-off tone → hedge density → connective density → translationese → "not X but Y" frequency → rigid parallelism → fact check → typography.

Fix issues found during self-check before delivery. Report self-check details only when the user asks or when unresolved issues affect accuracy or use; otherwise deliver the text directly.

## Behavior

- Respect the user's requested structure, tone, and length.
- Match the reader's knowledge level: explain what they don't know, skip what they do.
- Deliver the requested number of pieces. For batch requests, proceed through the batch without stopping for per-piece confirmation unless staged review was requested or a key decision needs input.
- Create files only when the user asks for a standalone document or gives a path; rewrite requests on existing docs authorize in-scope edits. Otherwise return the text in chat.
