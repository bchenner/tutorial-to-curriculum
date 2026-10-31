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
3. **GUI-to-Code Translation**: Show both how the human instructor did it in the GUI/add-on AND the exact, copy-pasteable programmatic code (`bpy`, TypeScript, etc.) that achieves the identical result headlessly.
4. **Common Pitfalls & Anti-Patterns**: What goes wrong (e.g., deleting parent empties without `CLEAR_KEEP_TRANSFORM`, using Time Stretching after keyframing, denoising *after* camera DoF) and how to prevent it.

---

## Step 4: Build the Multi-Tier Assessment Suite & Diagnostic Scoring System

Create a complete assessment package in `ref_<slug>/assessments/` (or inside the manual + dedicated test files) consisting of **two versions of every assessment**:
- **Student Version (for the AI)**: Contains only the prompts, reference image strips, starter specifications, and required deliverable format (no answers).
- **Instructor / User Grading Guide**: Contains the answer keys, point breakdowns, visual inspection checklists, automated grading script usage, and **Diagnostic Error-Routing Tables**.

### Required Assessments
1. **Modular Quizzes (Quizzes 1–4)**:
   - Short, targeted quizzes after major module groups (e.g., Quiz 1: Asset Pipeline, Scale & Shaders `[M1–M2]`; Quiz 2: Rigging & Facial Systems `[M3]`; Quiz 3: Stop-Motion Animation, Walk Cycles & Dynamic Parenting `[M4]`; Quiz 4: Cinematography, Rendering & Nuke/Blender Compositing `[M5–M6]`).
   - Mix multiple-choice, visual diagnosis ("Look at `crops/cNN.png` — which node/bone setting is wrong and why?"), and short code-snippet debugging.
2. **Final Test 1 — The 1:1 Replication (Copy) Test**:
   - Extract a short (3–6 second) showcase sequence from the playlist into a **frame-by-frame screenshot strip (`sheets/test1_copy_reference_strip.jpg`)** + exact frame timing sheet (`assessments/test1_spec.json`).
   - Task the AI to write a script that reproduces the camera framing, lighting, rig poses, keyframe timing, camera cuts/zooms, and compositing 1:1.
3. **Final Test 2 — The Creative & Tactical Execution Test**:
   - Give the AI a brand-new scenario (new narrative beat, character interaction, prop handoff, locomotion + camera move + VFX) that requires combining *all* modules `[M1–M6]` creatively without a frame-by-frame template to copy.
4. **Automated + Human Rubric & Diagnostic Scoring System (`100-Point Scale`)**:
   - Provide an automated verification script (e.g., `grade_submission.py`) that inspects the AI's generated `.blend` / code / rendered frames for objective parameters (FPS, clip end, F-stop, bone hierarchy, keyframe interpolation types, compositor nodes) and outputs a structured score report.
   - Pair every rubric row with its **`[M#.#]` Remediation Pointer** so any deduction (< full points) immediately tells both the User and the AI student:
     - **What failed**
     - **Which exact `[M#.#]` section & screenshot to re-read**
     - **How to fix it on retry**

### Diagnostic Score Bands
| Score Range | Tier | Meaning & Next Action |
|---|---|---|
| **90–100** | `S / Master` | Production-ready mastery. Proceed to autonomous production. |
| **80–89** | `A / Proficient` | Minor polish issues. Review flagged `[M#.#]` tags and patch specific parameters. |
| **65–79** | `B / Developing` | Core mechanics work, but style-defining rules broke. Re-read flagged `[M#.#]` modules + visual sheets and re-run failed section. |
| **< 65** | `F / Retrain` | Fundamental pipeline failure. Re-study all failed module groups and retake the corresponding Modular Quiz before retrying the Final Test. |
