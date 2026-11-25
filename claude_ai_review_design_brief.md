# Design & Engineering Brief: *Servo-Gate & Sensor Sprint* (`servo_gate_sensor_sprint.html`)

## 1. Purpose & Target Audience
* **Product:** A zero-dependency, single-file HTML/CSS/JS educational robotics simulator (`servo_gate_sensor_sprint.html`) designed for **Grade 8 and Grade 9 Coding & Robotics** classrooms.
* **Core Learning Loop:** `Sense → Decide → Act`. Learners program a 2D shape-robot using visual block-like control structures rather than manual arrow-key driving, connecting virtual sensors (`Ultrasonic`, `IR Color`, `LDR Light`, `Touch Bumper`) and actuators (`DC Motors`, `180° Positional Servo`, `360° Continuous Servo`, `High-Beam LED`, `Piezo Buzzer`, `Electromagnet`) to deterministic arena outcomes.
* **Environment Constraints:** Must run offline or online in any modern browser from a **single `.html` file** with no external build steps, no `alert()`/`confirm()` dialogs, and full light/dark theme support.

---

## 2. Current Architecture & Key Mechanics
1. **Deterministic Grid Simulation Engine:**
   * Arena grid: `13` columns (`x: 0..12`) × `8` rows (`y: 0..7`), with four cardinal headings (`0 = East`, `1 = South`, `2 = West`, `3 = North`).
   * **Strict Collision Guard (`isCellBlocked`):** Prevents the robot from entering out-of-bounds tiles, walls (`#`), closed `180°` servo gates, closed `360°` rotary winch doors, unlit optical LED gates, un-delivered cargo interlock gates, or un-swept debris. Blocked `DRIVE` attempts trigger bumper state, increment `collisions`, apply a visual bounce offset (`bumpOffsetX`/`bumpOffsetY`), and halt or branch into bumper logic cleanly without ever overlapping a solid barrier.
2. **8 Progressive Curriculum Levels + Custom Level Editor:**
   * **Grade 8:** Level 1 (Wall Stop & Bumper Safety), Level 2 (180° Positional Servo Gate), Level 3 (IR Line Follower & Color Junction), Level 4 (360° Continuous Servo Sweeper).
   * **Grade 9:** Level 5 (LDR Light-Activated Beacon Tunnel), Level 6 (Magnetic Cargo Transporter), Level 7 (Dual-Servo Automated Airlock), Level 8 (Autonomous Search & Rescue Gauntlet).
   * Every level includes a **verified reference solution** (`solution` array) that passes deterministically when executed.
   * **Teacher Level Editor:** Click-to-paint grid brushes (`Start` with direction cycling, `Goal`, `Wall`, colored floor tiles, `Dark Zone`, `180° Gate`, `360° Door`, `LED Gate`, `Steel Crate`, `Debris`, `Erase`), editable metadata, and JSON import/export.
3. **Hardware Configurator & Port Wiring:**
   * Supports `locked` (preset puzzle) vs. `adjustable` (teacher/student in-play or pre-run adjustment) modes per level, port labels (`S1`–`S4`, `M1/M2`, `SV1`, `SV2`, `OUT1`, `OUT2`), and Wikipedia reference links for each hardware module.
4. **Assessment Export & Teacher Grading:**
   * Generates a structured JSON payload containing student metadata, per-level status, block counts vs. par, steps, collisions, star ratings, equipped hardware, block programs, and a deterministic 8-character FNV-1a `integrityChecksum`.

---

## 3. Specific Areas for Claude.ai Review & Improvement

### A. Fixed Time Limit & Formal Assessment Mode (High Priority)
* **Countdown Timer:** Add an optional configurable assessment timer (e.g., `Off`, `5 min`, `10 min`, `15 min`, `20 min`, `30 min`, or custom minutes) with live display in the top bar / telemetry strip (`MM:SS`) and visual urgency warnings in the final 60 seconds.
* **Test Lock Controls:** Allow a teacher/test mode toggle that hides or locks the **Reference Solution** button, **Logic Hint** button, and **Level Editor** during active timed testing, while recording `timeLimitSec`, `timeElapsedSec`, `timeRemainingSec`, and `autoSubmittedOnTimeout`.
* **Auto-Lock / Auto-Submit:** When the countdown reaches `00:00`, stop any running execution, lock further edits/runs, and automatically generate/download or display the final assessment JSON package.

### B. Expanded Assessment & Rubric Considerations
When refining the assessment workflow, consider these pedagogical and practical classroom requirements:
1. **Multi-Criteria Rubric Scoring:** Beyond pass/fail and star count, score learners across explicit Grade 8/9 competencies:
   * *Sensor & Condition Appropriateness* (e.g., using distance/light/color/bumper conditions rather than hardcoding blind turns where dynamic obstacles apply).
   * *Actuator & Servo Precision* (e.g., matching `180°` angles vs. `360°` CW/CCW/STOP commands and electromagnet/LED states).
   * *Code Efficiency vs. Par* (`blocksUsed` vs. `parBlocks`) and *Safety / Collision Avoidance* (`collisions === 0`).
2. **Attempt & Edit Telemetry:** Track `attempts` per level, whether `solutionViewed` or `hintViewed` was triggered during the session, and `firstPassSuccess` so teachers can distinguish independent mastery from guided retries.
3. **Batch / Multi-Student `.json` Grading:** In the Teacher Grading tab, allow selecting **multiple `.json` files at once** (`<input type="file" multiple>`) to render a comparative class summary table (student name, class, score/stars, levels passed, time used, checksum validity, and one-click replay).
4. **Offline & Online Submission Ergonomics:**
   * **Offline:** Single-click `.json` download with clear naming (`assessment_<Class>_<Student>_<Date>.json`) and optional CSV summary export for spreadsheet gradebooks.
   * **Online:** Compact/formatted JSON copy + optional webhook/LMS paste instructions so students can submit via Google Forms, Moodle, Canvas, or Teams.
5. **Tamper Awareness & Academic Integrity:** Keep the FNV-1a checksum plus `submittedAt` and `sessionId`, while clearly labeling it as a classroom integrity check (since pure client-side JS cannot provide cryptographic secret-key signing without a server).

### C. Codebase & UX Polish Opportunities
* **Nested / Compound Control Flow:** Verify indentation, `ELSE` / `END_IF` pairing, and `REPEAT_GOAL` / `END_REPEAT` loop nesting so learners can construct clear multi-branch Grade 9 programs without subtle control-flow edge cases.
* **Accessibility & Responsiveness:** Ensure keyboard focus states, touch target sizes, and mobile/tablet layouts remain clean without horizontal overflow.
* **Maintainability:** Keep the single-file structure self-contained, modularly commented, and free of `alert()` or `confirm()`.

---

## 4. Requested Deliverable from Claude.ai
Please review `servo_gate_sensor_sprint.html` (and the companion patch below) and produce an improved, complete, runnable `servo_gate_sensor_sprint.html` that:
1. Preserves all existing features (Dark/Light mode, 8 levels + verified solutions, strict non-wall-entry collision physics, Chassis Builder with 180°/360° servos & Wikipedia links, Teacher Level Editor, and JSON import/export).
2. Adds **Fixed Time Limit / Assessment Test Mode** (countdown timer, test lock for hints/solutions/editor, attempt & hint tracking, and auto-lock/auto-submit on timeout).
3. Upgrades **Student Assessment & Multi-Student Teacher Grading** with rubric breakdown, offline `.json` + CSV export, online JSON copy, and batch `.json` file inspection.