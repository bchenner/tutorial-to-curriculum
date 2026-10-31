---
name: tutorial-to-curriculum
description: >-
  Convert any YouTube tutorial video or playlist into a complete, visual-first AI training curriculum,
  tactical execution manual (with hybrid contact-sheet + high-res crop screenshots), modular quizzes,
  a 1:1 replication test, a creative execution test, and an error-routing diagnostic rubric.
  Use when the user asks to learn from a YouTube playlist/tutorial, compile training data for an AI
  from videos, or run "/tutorial-to-curriculum <url>".
---

# `/tutorial-to-curriculum` — Video Playlist to AI Training & Assessment Pipeline

Convert a video tutorial or multi-video playlist into a self-contained, screenshot-backed technical manual and diagnostic testing suite designed for text+image AI models (such as Claude) that cannot watch raw video files.

## Usage

```text
/tutorial-to-curriculum "<youtube-video-or-playlist-url>" --slug <name> [--target <execution-env>]
```

- `--slug`: Folder slug under `ref_<slug>/` and `docs/<slug>-training-manual.md`.
- `--target`: How the AI student will execute the learned skill (e.g., `bpy` for headless Blender Python, `remotion` for React/Remotion, `code` for general programming).

---

## Step 0: Clarify Execution Target & Tool Translation Upfront

Before downloading gigabytes of video, ask the user (in ONE `ask_question` call if unspecified):
1. **Execution Environment**: Will the AI student execute via code/scripts (e.g., `bpy` Python, React/Three.js) or GUI steps?
2. **External/Paid Tool Handling**: If the playlist uses external GUI apps or paid add-ons, should they be translated into self-contained programmatic equivalents?
3. **Screenshot Token Budget**: Hybrid (compact `480×270` contact sheets for motion/progression + high-res `1280×720` crops for readable node graphs/UI text) is recommended.

---

## Step 1: Enumerate Playlist & Extract Transcripts + Metadata

1. Create workspace directories:
   - `ref_<slug>/transcripts/` — `.info.json`, `.vtt`, and cleaned `[HH:MM:SS]` `.txt` transcripts.
   - `ref_<slug>/videos/` — `720p` MP4 files for frame extraction.
   - `ref_<slug>/sheets/` — `480×270` tiled contact sheets (`3×4` or `3×3` grids with `[MM:SS]` caption bars).
   - `ref_<slug>/crops/` — High-resolution (`1280×720+`) crops of node trees, shader graphs, UI parameters, and before/after comparisons.
   - `ref_<slug>/assessments/` — Quizzes, Copy Test, Creative Test, User Grading Guide, and Automated Grader scripts.
2. Run `yt-dlp --flat-playlist --dump-json "<url>"` to list all video IDs, titles, durations, and channels.
3. Download metadata and English subtitles (`--write-info-json --write-subs --write-auto-subs --sub-langs "en-orig,en"`).
4. Strip VTT formatting tags and deduplicate rolling caption lines into timestamped `[HH:MM:SS] text` files.
5. If any video lacks subtitles on YouTube, flag it for denser visual frame sampling (every 15–30 seconds + chapter boundaries).

---

## Step 2: Download Videos & Extract Hybrid Visual Evidence

Because the target AI (e.g., Claude) cannot watch `.mp4` files, **every visual concept, UI panel, node graph, and animation beat must be converted into static images**:

1. **Download 720p MP4s in parallel** (`max_workers=4` with `yt-dlp -f "bestvideo[height<=720][ext=mp4]+bestaudio[ext=m4a]/best[height<=720]"`).
2. **Generate Per-Video Overview Contact Sheets (`ref_<slug>/sheets/vNN_<topic>.jpg`)**:
   - Extract 9–12 key timestamps per video aligned to chapters and transcript milestones.
   - Scale each tile to `480×270` (preserving aspect ratio with padding) and burn a bottom label bar: `[MM:SS] Step description`.
   - Always pass `["ffmpeg", "-nostdin", "-v", "error", "-y", "-ss", ..., "-frames:v", "1", "-update", "1"]` when extracting single frames on Windows so FFmpeg never blocks on stdin/pipes or image2 sequence patterns.
3. **Generate Tactical Motion Progression Sheets (`ref_<slug>/sheets/tactical_<mechanic>.jpg`)**:
   - For any multi-frame animation mechanic (e.g., walk cycles, physics snaps, camera cuts, build animations), extract a tight frame-by-frame sequence showing exact pose deltas across frames.
4. **Generate High-Res Readable Crops (`ref_<slug>/crops/cNN_<detail>.png`)**:
   - Whenever a tutorial shows a node graph, shader network, modifier stack, or fine-print rule chart that would be illegible at `480×270`, extract a full-resolution (`1280×720` or `1920×1080`) frame or targeted crop.
5. **Visually Inspect Every Generated Sheet & Crop**:
   - Read the generated `.jpg` / `.png` files using `read_file` to verify that timestamps didn't land on transition fades, sponsor reads, or blurry frames. Re-extract any frame that isn't crystal clear.

---

## Step 3: Compile the Modular Tactical Training Manual (`docs/<slug>-training-manual.md`)

Structure the manual into **numbered diagnostic modules (`M1` through `M6`) and sub-rules (`[M1.1]`, `[M1.2]`, ...)**. Every `[M#.#]` ID serves as a **Diagnostic Routing Tag** used by the quizzes, tests, and grading rubrics.

For each `[M#.#]` section, include:
1. **The Tactical Rule & Physical/Visual "Why"**: Exact numeric values (e.g., focal lengths, f-stops, frame counts, wattage, node math) and *why* the style breaks if you violate them.
2. **Visual Proof**: Embedded/linked contact sheets (`ref_<slug>/sheets/...`) and high-res crops (`ref_<slug>/crops/...`) with tile-by-tile commentary.
3. **Tactical Workflow & Architecture Specification (NO Copy-Paste Solution Scripts in Student Manual)**:
   - Document the exact node connections, bone hierarchies, mathematical rules, and step-by-step operations from the tutorial **without** placing pre-written solution scripts in the student manual/PDF. The AI student must synthesize the visual sheets and tactical rules to author its own scripts from scratch during the Quizzes and Final Tests.
   - Store all reference code implementations inside the **Instructor Grading Guide (`user_grading_guide_and_rubrics.md`)** as the gold-standard solution key.
4. **Common Pitfalls & Anti-Patterns**: What goes wrong (e.g., deleting parent empties without `CLEAR_KEEP_TRANSFORM`, using Time Stretching after keyframing, denoising *after* camera DoF) and how to prevent it.
5. **Final Module (`[M7]`): Autonomous Idea-to-Video Production Playbook**:
   - Always conclude the manual with a post-graduation production playbook (`Beat-Sheet & Frame Budgeting -> Modular Asset/Rig Generation -> Self-QC Pre-Flight Check -> Crash-Safe PNG Sequence Render + FFmpeg .mp4 Compilation`) so that once the AI passes the curriculum, the user can simply hand it a 1-sentence story idea and receive a finished, playable `.mp4` video in that style.

---

## Step 4: Build the Multi-Tier Output-Quality Assessment Suite, Batch-Review Schedule & Diagnostic Rubric

Create a complete assessment package in `ref_<slug>/assessments/` (plus a unified student `.md` and `.pdf` bundle in `docs/`) consisting of **two versions of every assessment**:
- **Student Version (for the AI)**: Contains the practical output-quality quizzes, the mandatory **Batch-Review Dependency Schedule**, reference image strips, and final test specifications (zero answer keys or pre-written solution code).
- **Instructor / User Grading Guide**: Contains the batch-review instructions, visual + programmatic rubrics, reference solution code library, automated grading script usage, and **Diagnostic Error-Routing Tables**.

### Required Assessments & Batch-Review Architecture
1. **Practical Output-Quality Quizzes (Quizzes 1–4 — NOT Text Trivia)**:
   - Quizzes must be **hands-on output-quality challenges** where the AI writes a script to recreate a model/shader (`Quiz 1`), rig/pose characters & props (`Quiz 2`), animate locomotion/physics (`Quiz 3`), or light/composite a shot (`Quiz 4`), outputting both a scene file and a rendered verification sheet for the user to grade.
   - **Mandatory Dependency Graph & Batch-Review Schedule (`STOP FOR USER REVIEW` Gates)**:
     - Map out which quizzes are foundational prerequisites vs. which quizzes are independent and can be executed/reviewed in parallel batches:
       - **Batch 1 (Foundational Gate)**: `Quiz 1` (Models, Scale, Materials). *AI must stop and wait for user review before proceeding.*
       - **Batch 2 (Parallel Independent Systems)**: e.g., `Quiz 2` (Rigging) + `Quiz 4` (Optics & Compositing) if both depend on `Quiz 1` but not on each other. *AI executes both in one batch, then stops for user batch review.*
       - **Batch 3 (Dependent Synthesis Gate)**: e.g., `Quiz 3` (Walk Cycle & Dynamic Physics, which requires the verified rig from `Quiz 2`). *AI stops for user review before unlocking the Final Tests.*
2. **Final Test 1 — The 1:1 Source Replication (Copy) Test (`100 Points`)**:
   - Extract both the **Final Rendered Showcase Strip (`sheets/test1_rendered_target_strip.jpg`)** and the **Viewport Breakdown Strip (`sheets/test1_copy_reference_strip.jpg`)** from a showcase clip in the playlist.
   - Task the AI to visually inspect the source strips and replicate the set, custom markings, lighting, camera framing, poses, cuts, and compositing 1:1, outputting a `.blend` scene, a `3×3` side-by-side comparison sheet, and a playable `.mp4` video.
3. **Final Test 2 — The Creative & Principle-Mastery Test (`100 Points`)**:
   - Give the AI an **open-ended creative brief** (the AI invents its own original story beat, setting, and characters) and grade it on how faithfully it applies the **Core Style Principles (`[M1]`–`[M7]`)** without a reference template to copy, outputting a `.blend` scene, a `3×3` storyboard sheet, and a finished playable `.mp4` short film.
4. **Automated + Human Rubric & Diagnostic Scoring System (`100-Point Scale`)**:
   - Provide an automated verification script (`grade_submission.py --quiz <1-4>` and `--test <1-2>`) that inspects the AI's generated scene/code and outputs a structured score report.
   - Pair every rubric row with its **`[M#.#]` Remediation Pointer** so any deduction immediately tells both the User and the AI student:
     - **What failed**
     - **Which exact `[M#.#]` section & screenshot to re-read**
     - **How to fix it before unlocking the next review batch**

### Diagnostic Score Bands
| Score Range | Tier | Meaning & Next Action |
|---|---|---|
| **90–100** | `S / Master` | Production-ready mastery. Proceed to autonomous production. |
| **80–89** | `A / Proficient` | Minor polish issues. Review flagged `[M#.#]` tags and patch specific parameters. |
| **65–79** | `B / Developing` | Core mechanics work, but style-defining rules broke. Re-read flagged `[M#.#]` modules + visual sheets and re-run failed section. |
| **< 65** | `F / Retrain` | Fundamental pipeline failure. Re-study all failed module groups and retake the corresponding Modular Quiz before retrying the Final Test. |
