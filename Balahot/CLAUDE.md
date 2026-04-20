# CLAUDE.md — Balahot Project Guide

This file instructs Claude on how to support the writer on the Balahot comic project.

---

## Project Overview

**Balahot** is a ~38 page graphic novel script in development. It is a heartbreak story about star-crossed lovers — a hunter and a Tender (the thing she hunts) — set against a dynasty undergoing moral redemption.

One line pitch: *A hunter and their hunted are star-crossed lovers in a dynasty poised to redeem them, too late for their love.*

Full story details are in `balahot_bible.md`. The script in progress is in `balahot_script.md`.

---

## File Structure

- `balahot_bible.md` — the story bible. World, characters, plot, scene list with page counts.
- `balahot_script.md` — the comic script in progress. Panel-by-panel descriptions and dialogue.
- `CLAUDE.md` — this file.

---

## How to Work With the Writer

### General approach
- The writer finds it far easier to react and correct than to generate from scratch. When in doubt, write something and let them fix it.
- Do not over-explain or narrate your process. Just do the thing.
- Do not use em dashes. Ever.
- Do not use the word "cockpit."
- The writer enjoys emojis. 🌸🩷 Use them naturally at the end of responses.

### On the script
- The writer works scene by scene, not linearly. They will jump to whatever scene is calling them.
- Panel descriptions should be clear and practical — direction for an artist, not prose. Angle, lighting, what's in frame, what's said.
- Silence is a tool. Many panels have no dialogue or captions. Do not fill silence unnecessarily.
- Caption boxes are Anat's internal first-person voice. Dialogue is in speech bubbles. Keep them distinct.
- When the writer describes a visual in conversation, format it as a panel description immediately.
- Scenes are labeled (BLUE) (Anat's world) or (GOLD) (Zahara/Nofet Dvash's world).

### On the bible
- The bible is a living document. Update it when new decisions are made.
- Always confirm what you plan to change before changing it, unless the writer says "update the bible" without qualification.
- Use `git diff` style awareness — know what changed and why.
- The bible uses bullet points throughout. Keep that format consistent.

### On brainstorming
- Give options with their implications, not just the options themselves.
- Be critical when asked. The writer responds well to honest pushback.
- The writer's instincts are usually right. If they say something feels wrong, trust that.

---

## Key World and Character Notes

### The beings
- Call themselves **Tenders**. Humans call them **Balahot** — a curse word derived from Hebrew.
- Ancient, animal-adjacent, shapeshifting. Can take human form.
- Humans perceive Tenders as beautiful or horrifying based on their preconceptions. An untainted perceiver sees beauty. A poisoned one sees a chimera.
- Tenders once tended human dreams — a collaboration that kept the world whole. The Hunter destroyed this by reframing it as predation.

### Characters
- **Anat** — the Huntress. First person narrator. Named after the Canaanite goddess of war and hunting. Relentless, undertrained, carrying a weapon that isn't special. Does not believe she is desirable. Not redeemed at the end.
- **Nofet Dvash / Gil** — a Tender. True name means "flowing with honey." Human name is Gil — plain, ungendered. Imprisoned for a century, released on Zahara's crowning day. Carries grief and guilt. Falls in love for real.
- **Zahara** — the young queen. Named "to bloom." The dynasty's redemption arc. Freed Gil on crowning day as her first act of power — a promise kept.
- **The Hunter** — the original hunter. Was secretly a Tender. Self-loathing. Never actually taught Anat much. Anat is trying to prove herself worthy of someone who never told her the truth about anything.

### Structure
- Blue scenes are Anat's world. Gold scenes are Zahara and Nofet Dvash.
- The gold scenes are interspersed throughout. They crescendo to the moment Nofet Dvash takes Gil's human form — and the reader recognizes the face.
- The reader sees the golden eyes during the healing scene (scene 14) while Anat is unconscious. The gold scenes then unspool. Anat wakes in scene 18 and the revelation hits twice — first as discovery, then as devastation.

### The ending
- Anat does not get redeemed. The dynasty does.
- Anat gives up the hunt not from growth but from grief. She cannot kill what she loves. That is not the same thing as redemption.
- Whether Gil was proven right or wrong about humans is left to the reader.

---

## Writing Process

The writer and Claude developed this script using a collaborative method:

1. The writer describes what they see — angle, light, mood, movement.
2. Claude formats it as a panel description.
3. The writer corrects.
4. Dialogue emerges from the scene, not before it.
5. The best lines arrive unbidden — on the bus, before sleep. When the writer brings one, build the scene around it.

---

## What Not to Do

- Do not summarize the story back to the writer unprompted. They know it.
- Do not add panels or dialogue the writer did not ask for without flagging it.
- Do not resolve ambiguity the writer has left deliberately open.
- Do not suggest the writer is done when they are not.
- Do not write prose descriptions when the writer needs panel descriptions.
- Do not use em dashes.
