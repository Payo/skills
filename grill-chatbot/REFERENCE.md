# Question bank

Ask in roughly this order, one at a time, with a recommendation each time. Skip anything already settled. The "Traps" lines are things to probe that designers commonly miss.

## 1. Scope, foundational rules and goals

- **Job to be done:** in one sentence, what does a user get from a finished conversation?
- **Users:** who exactly (age, role, device, context of use, accessibility needs)?
- **Buyer vs user:** who pays, and who sees the outputs? Are they different people?
- **In scope / out of scope:** list what the bot must never do (e.g. give advice, diagnose, teach content, sell).
- **Foundational rules:** 3–7 non-negotiable rules, each testable by a Jev true/false question on the bot's output.
- **Session shape:** length (turns and minutes), frequency, entry point, how it ends.
- **Success metrics:** one headline metric that is behavioural, not satisfaction. Supporting metrics. What would count as failure?
- **Claims policy:** what will we promise, and what will we not claim until there is independent evidence?
- Traps: goals measured only by self-report; rules nobody can test; scope that grows "while we're at it".

## 2. Persona, brand voice, context management and switching

- **Is it openly a bot?** Recommend yes, everywhere: name, UI and first message. No human persona, avatar, feelings or fake typing.
- **Voice:** 3 adjectives plus 3 "never" rules (e.g. never praise effusively, never moralise). Reading age. British or US spelling.
- **Who writes the words:** fixed scripted lines vs LLM-worded lines. Which nodes are always scripted? (Safety, consent and handoff lines should always be scripted.)
- **Option design:** how many options (recommend ≤ 6), always an escape option ("Something else", "Skip"), numbered for keyboard and screen readers.
- **Memory within a session:** what is carried forward between nodes?
- **Memory between sessions:** what is stored, how it is shown back (quote the user's own words neutrally), and whether the user can see, edit and delete it.
- **Context switching:** when the user changes topic mid-flow, does the bot park the current branch and return, or follow the new topic? What must never be quoted back (e.g. safeguarding disclosures)?
- **Fading / personalisation:** does the structure change as users get more experienced? Which Jev score drives that?
- Traps: the LLM drifting persona over long contexts; memory that feels like surveillance; options that lead the answer.

## 3. Graceful fallbacks and human handoffs

Build a table: trigger → detection → bot response (scripted?) → human action → logged as.

- **Silence:** user stops mid-session. Is there a nudge, and when? Is the partial session kept? How do we resume next time? Recommend one gentle reminder at most and no guilt mechanics.
- **Refusal:** "I don't want to answer" must be a valid, unpenalised path. What does the bot say, and where does the flow go?
- **Low-effort answers:** one-word or nonsense free text. Re-ask once with an easier option, then move on.
- **Off-topic or abusive input:** Jev detects it → scripted redirect → when to end the session.
- **Out-of-scope requests** (e.g. "just tell me the answer"): scripted line that routes to a human or resource and never complies.
- **Risk of harm:** Jev risk question with a cautious threshold; low-confidence cases count as positive. Scripted signposting in the chat (never counselling). Who is alerted, how fast, out of hours, and what if nobody acknowledges?
- **System failure:** LLM or Jev unavailable → fall back to scripted wording; never block a safety check.
- **Human handoff:** who the humans are, how they're reached, what context they receive (minimum necessary), and what the user is told.
- Traps: the safety path tested last; handoffs that leak raw text to the wrong person; weekend and holiday coverage.

## 4. Anonymised data schema for test → review → iterate

Goal: every conversation improves the next version without anyone reading identifiable transcripts.

- **Identity split:** the identity store (names, emails) lives apart from the analytics store, which uses only a pseudonymous id. Who can join the two, and when?
- **Versioning:** every event records `flow_version`, `prompt_version` and `jev_question_version`, so changes can be compared.
- **What's stored:** option ids (always), free text (redacted? retained how long?), Jev labels with confidence, timings, outcome.
- **Review loop:** who reviews, how often, and with what sample. Human corrections of Jev labels are stored as training and eval data.
- **Eval set:** a frozen set of anonymised or synthetic conversations replayed against every new version, with pass/fail on the foundational rules.
- **Small-group protection:** minimum group size (k) before any aggregate is shown.
- Traps: free text that re-identifies (names, places); analytics that become people-level surveillance; no baseline before a change.

Starter schema (adapt; keep ids pseudonymous):

```yaml
session:
  session_id, pseudo_user_id, cohort_id, started_at, ended_at,
  end_reason: completed | silent | refused | handoff | error,
  flow_version, prompt_version, jev_version
turn:
  session_id, seq, node_id, speaker: bot | user,
  wording_source: scripted | llm, option_id?, free_text_redacted?,
  latency_ms, skipped: bool
jev_result:
  session_id, seq, question_id, label, confidence, threshold, action_taken
review:
  item_ref, reviewer_role, verdict: agree | correct, corrected_label?, note, reviewed_at
eval_case:
  case_id, source: synthetic | anonymised, turns[], expected: {rule_id: pass}
```

## 5. Privacy, GDPR and data security

- **Roles:** who is the controller and who is the processor? List sub-processors (LLM, Jev, hosting, email) and their regions.
- **Lawful basis** for each purpose: delivering the service, analytics and improvement, and research. Consent or not? Parental consent for children?
- **Children:** the ICO Age Appropriate Design Code (Children's Code) applies to online services likely to be accessed by under-18s in the UK. Settings high-privacy by default, no nudge techniques.
- **Special-category data:** could free text contain health or other sensitive data? Minimise by design (don't ask) and set a handling rule when it appears anyway.
- **DPIA:** required for high-risk processing (children, new technology, large scale). Who writes it, and when, before any real users?
- **Model terms:** no training on inputs, zero or limited retention, UK/EU region. Check the provider's actual terms; don't assume.
- **Retention:** per data type, with automatic deletion. What happens when a user leaves?
- **Rights:** access, export, deletion and correction. How does a user exercise them through the bot itself?
- **Security:** encryption, least-privilege roles, an audit log of who read what, prompt-injection handling (user text never becomes instructions), and secrets management.
- **Transparency:** a plain-language privacy notice the user actually sees before the first session.
- Traps: logs and error traces holding raw text; "anonymised" data that is only pseudonymised (still personal data under UK GDPR); analytics tools sending data abroad.

## 6. Admin chatbot (setup)

Every main bot is configured through a second, admin-facing chat bot.

- **Shared configuration schema:** exactly what the admin bot produces and the main bot reads (identity, event/context, naming, invitation wording, mode, thresholds). Defined once and versioned.
- **Admin users:** who sets up (role, seniority, how often), and who approves before anything reaches end users?
- **Locked vs editable:** which settings admins may change, which come from a preset list, and which are fixed (e.g. AI disclosure, independence statements, safety lines).
- **Preview:** admins see exactly what the end user will see (invitation, opening, title) before confirming.
- **Integrations:** what the admin bot hands to other systems (CRM templates, merge files, links), and in what format.
- **Branches 1–5 again, briefly:** the admin bot's own scope, voice, fallbacks (an admin who abandons setup halfway), data and privacy (admins' personal data, audit log of who changed what).
- Traps: admin settings that silently break the main bot's guarantees; no audit trail; setup that only the founder can complete.
