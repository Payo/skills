# Court Clerk — Reference

## State machine

| From | To | Who | Meaning |
|---|---|---|---|
| — | `open` | clerk (intake) | Filed, precedent attached, awaiting hearing |
| `open` | `ruled` | adjudicator | Verdict + reasoning + remedy recorded |
| `ruled` | `applied` | clerk (application) | Remedy executed AND confirmed |
| `ruled` | `apply_failed` | clerk (application) | Remedy unexecutable; reason recorded |
| `applied` / `apply_failed` | `ruled` | adjudicator | Re-ruling (corrected verdict/remedy) |
| `applied` / `apply_failed` / `ruled` | `open` | adjudicator | Verdict retracted pending more facts |

Illegal moves: the clerk never writes `ruled`; nothing skips from `open` to `applied`; no state is ever deleted — a wrong entry is corrected by a new transition, appended.

`verdict` (`upheld` / `dismissed` / `partial`) is set with the `→ ruled` transition and survives all later transitions; only a re-ruling replaces it.

## Case id scheme

`C-` + 5 chars from `ABCDEFGHJKMNPQRSTUVWXYZ23456789` (no 0/O/1/I/L — shame-proof to mistype, unambiguous to read aloud). Check `REGISTER.md` before assigning; regenerate on collision.

## Case file template — `cases/C-XXXXX.md`

```markdown
---
id: C-XXXXX
type: <org's dispute taxonomy, e.g. score | conduct | process>
status: open | ruled | applied | apply_failed
verdict: null | upheld | dismissed | partial
filed: YYYY-MM-DD
ruled_at: null | YYYY-MM-DD
applied_at: null | YYYY-MM-DD
rule_at_issue: "<section/clause reference into RULES.md>"
---

## Private — parties (NEVER published)
- Appellant: <name/handle + contact>
- Other parties: <...>
- Private evidence: <links/notes not for the public record>

## Filing (anonymised)
<The complaint in the appellant's own words, identities removed.>

## Precedent
- C-AAAAA — <one-line holding + relevance>
- C-BBBBB — <one-line holding + relevance>
- (or: "No precedent found — first impression.")

## Ruling (public)
**Verdict:** <upheld/dismissed/partial>
**Reasoning:** <adjudicator's words. If departing from cited precedent, the
distinction MUST be stated here.>
**Remedy:** <in general terms — direction, not joinable magnitudes>

## Application
- Pre-ruling state: <what the remedy displaced — enough to reverse it>
- Action taken: <what was executed, when, confirmed how>
- (on failure) apply_error: <reason>

## History
- YYYY-MM-DD filed (clerk)
- YYYY-MM-DD → ruled: <verdict> (adjudicator)
- YYYY-MM-DD → applied (clerk)
```

## REGISTER.md format

```markdown
# Case Register
| id | type | status | filed | ruled | holding |
|---|---|---|---|---|---|
| C-7KQ2M | score | applied | 2026-07-04 | 2026-07-07 | Late-window entry dismissed; window is fixed |
```

The `holding` column is the searchable one-line summary — write it like a law report headnote: the *principle*, not the story.

## Timing rule (adopt when it applies)

If rulings modify the output of a still-running process (a live scoring window, an open billing period, an in-flight review cycle), adopt the **settlement rule**: rulings are *recorded* anytime but *applied* only after the underlying process has settled. The machinery and the judge never hold the pen at the same time — this removes the entire class of "the process overwrote the ruling" and "the ruling clobbered the process" failures. A ruling waiting for settlement sits honestly in `ruled`.

## Adapting to an organisation

- **Taxonomy:** define 2–5 dispute `type`s in RULES.md; resist more until volume demands it.
- **Multiple adjudicators:** name them per-type in RULES.md; a case's hearing goes to its type's adjudicator. The clerk still never rules.
- **Appeal-of-the-appeal:** model as a new case citing the old one as precedent-under-challenge, heard by a different adjudicator. Don't build tiers until the first such case actually arrives.
- **Publication surface:** the casebook's public form can be served anywhere (a repo, a wiki, a web page). Publish case files' public sections only — never the frontmatter-adjacent Private block.
