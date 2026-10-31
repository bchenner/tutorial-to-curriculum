# Tutorial to Curriculum (`tutorial-to-curriculum`)

A **Claude Code & Agent Skill** that converts any YouTube tutorial video or multi-video playlist into a complete, visual-first AI training curriculum, tactical execution manual (backed by hybrid contact-sheet + high-res crop screenshots), modular quizzes, a 1:1 replication test, a creative execution test, and an automated `[M#.#]` error-routing diagnostic rubric.

Designed specifically to train text+image AI models (such as **Claude**) that cannot watch raw `.mp4` video streams by translating every GUI operation, node graph, shader network, and frame-by-frame motion beat into static annotated visual sheets + programmatic code (e.g., headless Blender `bpy`, Remotion/React, or Python).

---

## What It Does

When invoked with `/tutorial-to-curriculum "<youtube-url>"`, the agent executes a 5-stage pipeline:

1. **Clarifies Execution Target & Tool Translation (Step 0)**:
   - Confirms whether the target AI learner will execute via programmatic scripts (e.g., Blender `bpy`, TypeScript/Remotion) or GUI steps.
   - Translates paid or external tools (e.g., Nuke, paid add-ons) into self-contained programmatic equivalents while documenting original concepts.
2. **Enumerates Playlist, Metadata & Timestamped Transcripts (Step 1)**:
   - Uses `yt-dlp` to enumerate all videos, chapters, descriptions, and subtitles, cleaning rolling `.vtt` captions into `[HH:MM:SS]` transcripts.
3. **Extracts Hybrid Static Visual Evidence for Vision LLMs (Step 2)**:
   - **Overview Contact Sheets (`480×270` tiles in `3×4` grids)**: Captures key chapter milestones across every video with burned-in `[MM:SS]` labels.
   - **Tactical Progression Strips (`3×3` grids)**: Captures frame-by-frame motion mechanics (e.g., 14-frame walk cycles, physics impacts, camera cuts) and the **Final Test 1 (1:1 Copy) Reference Strip**.
   - **High-Resolution UI & Node Crops (`1280×720+`)**: Preserves crisp text legibility for shader trees, compositor node graphs, and design rule tables.
4. **Compiles the Diagnostic Tactical Manual (`[M1.1]` – `[M6.x]`) (Step 3)**:
   - Organizes every rule, constant, failure mode, and headless code implementation under a unique **`[M#.#]` Diagnostic Routing Tag** (and optionally compiles a single self-contained **PDF + Markdown** bundle for direct upload to Claude).
5. **Builds the Multi-Tier Assessment & Automated Grading Suite (Step 4)**:
   - **Student Copy (for the AI)**:
     - **4 Modular Quizzes** (`25 pts` each = `100 pts`)
     - **Final Test 1 (1:1 Copy Test — `100 pts`)**: Frame-by-frame replication from a timestamped screenshot strip.
     - **Final Test 2 (Creativity & Execution Test — `100 pts`)**: Original production task combining all `[M1]`–`[M6]` modules.
   - **Instructor Copy (for the User)**:
     - Complete Answer Keys, Point Rubrics, Rank Tiers (`S/A/B/F`), and **Copy-Pasteable `[M#.#]` Remediation Feedback Blocks**.
     - **Automated Grader Script (`grade_submission.py`)**: Inspects the AI's generated scene/code and outputs an itemized diagnostic scorecard routing every mistake back to the exact `[M#.#]` section and screenshot.

---

## Install

Copy the skill folder into your Claude Code or Agent skills directory:

```bash
# Personal (available across all projects)
git clone https://github.com/bchenner/tutorial-to-curriculum.git
cp -r tutorial-to-curriculum/skills/tutorial-to-curriculum ~/.claude/skills/

# Project-level (.claude/skills or .agents/skills)
mkdir -p .claude/skills .agents/skills
cp -r tutorial-to-curriculum/skills/tutorial-to-curriculum .claude/skills/
cp -r tutorial-to-curriculum/skills/tutorial-to-curriculum .agents/skills/
```

---

## Usage

In Claude Code or your agent chat:

```text
/tutorial-to-curriculum "https://www.youtube.com/playlist?list=..." --slug lego_blender --target bpy
```

Or ask naturally:
> *"Watch this playlist and compile a tactical training manual, screenshots, modular quizzes, a 1:1 copy test, a creative execution test, and a grading rubric for Claude using the `tutorial-to-curriculum` skill."*

---

## License

MIT
