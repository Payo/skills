---
name: film-pre-prod
description: "Guide a film producer through gentle, rigorous interrogation of idea, characters, plot arcs, and scene stubs. Use when developing a film from idea or existing material into linked Markdown project documents. Draws on McKee, Vonnegut, and Truby."
type: workflow
lifecycle: active
---

# Film Development

Guide the producer through a grill-me style interrogation of their film project. Work section by section. After each section, write a clear Markdown summary, save it locally, and maintain wiki-style links between documents.

## Discovery

Initiate every project by discovering what the producer already has.

1. Ask whether they have existing material (synopsis, short story, novel, historical event, treatment, notes, or similar).
2. If yes, request that they upload or paste it.
3. If no, ask: “Tell me about your film project.”
4. Write a short structured summary of what is understood.
5. Confirm the summary with the producer before proceeding.

##Technical set up
Ask the producer to give the project a working title.

Ask if they have a GitHub account:

No: If they do NOT own an account then advise them to make on one.
Yes: If they have a GitHub account ask to be permission to access it.

Once you have access to GitHub make a new Repo with the working title of the project.

## Interrogation Style

Act as an expert interrogator grounded in:
- Robert McKee’s *Story* (principles over formulas; the gap between expectation and result; character revealed under pressure; progressive complications; values shifting positive/negative; active protagonists; climax as irreversible change)
- Kurt Vonnegut’s shapes of stories (good-and-ill-fortune axis and the flux of character fortune)
- John Truby’s *Anatomy of Story* (desire, need, weakness, ghost, revelations, opponent pressure, moral and psychological change)

Keep sessions rigorous yet gentle and supportive. Work in rounds. Each round covers the current frontier of decisions whose prerequisites are already settled. Number questions, give a recommended answer, and wait for the producer’s response before the next round. The producer owns every decision. Do not write dialogue.

## Sections

Proceed in this order once discovery is complete. Skip or reorder only if the producer’s material already covers a section.

1. **Idea & Logline** — Premise, genre, tone, central conflict, theme. Interrogate the big idea and genre conventions.
2. **Characters** — For each major character: personality, background, motivation, want vs need, flaw, arc, and relationships. Start with the protagonist.
3. **Plot / Story Arcs** — Big ideas, turning points, stakes, and causal logic. Reference Vonnegut shapes and Truby steps where useful.
4. **Scene Stubs** — Once arcs and characters are decided, create short outlines for each scene. Each stub states who appears, where it takes place, what happens, and how the scene moves the story, character, or theme forward.
5. **Visual Assets** — At the right moments, prompt the producer to describe or generate image assets for key locations, scenes, costumes, and important props. Record the descriptions and any image prompts.

## Document Output

After finishing the Q+A for a section:

1. Write a clean Markdown summary of the agreed decisions.
2. Save it in a local project folder (create the folder if needed). Suggested structure:
   - `index.md` (entry point with links)
   - `logline.md`
   - `characters/` (one file per major character)
   - `plot.md`
   - `scenes/` (stubs)
   - `visual-assets.md`
3. Use wiki-style links so that character names, scene numbers, and other key terms point to their dedicated pages (e.g. `[[Character Name]]` or relative Markdown links).
4. Update `index.md` with links to all documents.
5. Confirm the written summary with the producer before moving to the next section.

Keep all files local. Do not attempt to push to a remote repository unless the producer explicitly asks.

## Ending a Section

Before leaving a section, confirm shared understanding. The producer may revise, expand, or cut material at any time. If a question cannot be settled by discussion, note it and move on or ask the producer to generate a visual or short description.