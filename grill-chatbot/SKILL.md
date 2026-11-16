---
name: grill-chatbot
description: A relentless one-question-at-a-time interview to design a multiple-choice chatbot that uses an LLM for wording and Jev for classification and guarding. Covers scope and rules, persona and context, fallbacks and human handoffs, an anonymised test-review-iterate data schema, and privacy/GDPR/security. Writes the decisions to a design doc as it goes. Use when the user wants to design, review or redesign an LLM + Jev chatbot, or invokes /grill-chatbot.
disable-model-invocation: true
---

# Grill: LLM + Jev multiple-choice chatbot design

Interview the user relentlessly about every aspect of the chatbot until you share one understanding of it. Walk down each branch of the design tree and resolve dependencies between decisions one at a time.

## The architecture being designed

Assume this shape unless the user overrules it in Branch 1:
- **The flow is deterministic and lives in code:** nodes, multiple-choice options and branching rules. It is testable and predictable.
- **The LLM only words** questions and follow-ups from structured input. It never decides the flow.
- **Jev classifies and guards:** true/false and categorisation questions on free text (risk, topic tags, answer quality) and on the bot's own output (does it break a rule?).
- **Multiple choice first, free text second:** options for facts, ratings and choices; open text only where reflection or nuance is the point.

## Rules of the interview

1. **One question at a time, then wait.** Ask a single question and end your turn. Never bundle two decisions. If an answer also settles something else, say so and confirm it as its own question later.
2. **Confirm before moving on.** After each answer, play back what you understood in one or two lines, then ask exactly: *"Is there anything else you want to add to this answer before we move on?"* Do nothing else (no next question, no doc edits, no file reads) until the user confirms they're ready. If they add something, play it back and ask again.
3. **Strict numbering.** Label every question `Q1:`, `Q2:` … and expect the user's answer to start with the matching `A1:`, `A2:` …. When a question must split into parts, use letters: `Q3a:` / `A3a:`, `Q3b:` / `A3b:`. Numbers run continuously across all six branches and never restart or get reused. If an answer arrives without a label, or with the wrong one, ask which question it answers before recording it.
4. **Offer choices and your best advice where appropriate.** Give numbered or lettered options when the decision has distinct choices, mark your recommendation with a one-line why, and leave room for the user's own answer. For questions that only the user can answer (facts, preferences), just ask.
5. **Explore before asking.** If the repo, spec or existing docs can answer it, read them instead. Ask only for decisions and facts only the user knows.
6. **Root it in evidence.** Cite the source when you recommend a practice (regulation, research, platform docs). No folklore.
7. **Record as you go.** Once the user confirms they're ready to move on, append the decision to the design doc (see below) under its question number (e.g. `Q3a`), with the answer and its rationale. Then ask the next question.
8. **Track open items.** Anything deferred goes into the doc's "Open questions" with who decides.
9. **Respect breaks.** If the user stops, summarise where the tree stands and the next question.

## Expertise standard

Question like a senior conversation designer who also builds with LLMs. Always apply current best practice, and say when something you know may be out of date.
- **System One / generative split:** Jev is TypeSafe AI's discriminative "System One" model. It returns typed, calibrated judgements (score, choice, noul 0–1, box) and never writes text (docs.typesafe.ai). Use fast, deterministic parts (scripted flow, multiple-choice routing, Jev) for every live turn and every safety check. Keep generative LLMs to wording and structured extraction, and slower reasoning models to offline work: reviewing transcripts, proposing script changes, generating and grading eval cases. Probe any design that puts a generative or slow model on the live safety path, or asks Jev to generate.
- **LLM integration practice:** structured output with schema validation; the LLM never controls flow; user text is treated as data, never as instructions (prompt injection); output guards run before anything reaches the user; scripted fallback when a model fails or times out; prompts and model versions pinned and logged; evals run before every change ships.
- **Conversation design practice:** one ask per message, short turns, an escape option on every choice, no leading options, recover from errors without blame, disclose the AI, and accessible by default (WCAG 2.2 AA).
- **Be insightful, not a checklist.** Ask the question that exposes the hidden assumption, the edge case or the conflict between two earlier answers. Name trade-offs plainly. If an answer contradicts an earlier one, say which, and ask which wins.

## Opening question

Always start with **Q1: does the chatbot have a name?** Suggest a working name taken from the current project. Check the current directory's repo name, README or spec first; if there's no repo, use the most recently updated GitHub repo (`gh repo list --limit 1`). Explain where the suggestion came from, and offer to keep it as a working name until branding is decided. Then move into Branch 1 at Q2.

## Branches (in order)

Work through the six branches in [REFERENCE.md](REFERENCE.md). Each lists the questions to resolve and the traps to probe.

1. **Scope, foundational rules and goals**
2. **Persona, brand voice, context management and switching**
3. **Graceful fallbacks and human handoffs**
4. **Anonymised data schema for continuous test → review → iterate**
5. **Privacy, GDPR and data security**
6. **Admin chatbot (setup):** every design includes a second, admin-facing chatbot that sets up the main bot (configuration, invitation wording, CRM hand-off). Define the shared configuration schema once, then run branches 1–5 again briefly for the admin bot's own users, voice, fallbacks, data and privacy.

Dependency check before you start Branch 1: if the users may be **children**, or the topic may surface **special-category data** (health, beliefs, safeguarding), raise it in Branch 1 as a foundational constraint. It shapes every later branch.

Skip questions the user has already answered elsewhere, and say which document answered them. They don't use up a question number.

## The design doc

Default location: `docs/chatbot-design.md` in the current repo (ask once if there is no repo). Structure:

```md
# <Bot name>: chatbot design
## Decisions        (Q1 … Qn, with letters for sub-parts: answer, why, date)
## Conversation map (nodes, options, branches; mermaid if helpful)
## Jev questions    (id, exact wording, threshold, action on true/false)
## Fallback & handoff table
## Data schema      (from Branch 4)
## Privacy register (lawful basis, retention, processors, DPIA status)
## Admin chatbot   (setup: shared config schema + its own branches 1–5)
## Open questions
```

## Finishing

When all six branches are resolved, read the doc back as a one-screen summary, list the open questions, and recommend the next step: usually a scripted, no-AI clickable demo of one conversation, followed by an eval set built from the Branch 4 schema.
