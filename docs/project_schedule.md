# OWL Glasses — Project Schedule (March 9 – April 30, 2026)

## How to Use This Document

- **5 team members** — nobody is pre-assigned
- Browse the categories: **Electronics**, **Software**, **CAD**, **Presentation/Documentation**
- Put your name in the `[ ]` checkbox next to any task you want to own
- Format: `[YourName]` — e.g., `[Alice]`
- Each person should aim to pick tasks across weeks so the workload stays balanced
- If a task needs two people, add both names: `[Alice, Bob]`

---

## Schedule Overview

| Week | Dates | Theme |
|------|-------|-------|
| 1 | Mar 9 – Mar 15 | Setup & Planning |
| 2 | Mar 16 – Mar 22 | Core Building Blocks |
| 3 | Mar 23 – Mar 29 | Component Integration |
| 4 | Mar 30 – Apr 5 | End-to-End Connection |
| 5 | Apr 6 – Apr 12 | Testing & Debugging |
| 6 | Apr 13 – Apr 19 | Polish & Demo Prep |
| 7 | Apr 20 – Apr 26 | Final Demo & Presentation |
| 8 | Apr 27 – Apr 30 | Buffer / Submission |

---

## Week 1 — Setup & Planning (Mar 9 – Mar 15)

### Electronics
- [ ] Order any remaining parts (microphone module, buttons, battery pack, wires)
- [ ] Verify ESP32-CAM board boots and connects via USB-to-serial programmer
- [ ] Flash a basic "hello world" / LED blink sketch to confirm toolchain works
- [ ] Test camera module — capture a single still image to serial monitor

### Software
- [ ] Set up Python project structure (`software/` folder, `requirements.txt`, virtual env)
- [ ] Choose and install face processing libraries (`face_recognition`, `OpenCV`, `dlib`)
- [ ] Set up `pytest` and `pytest-cov` for testing from day one
- [ ] Create a basic FastAPI or Flask skeleton app that returns "OK" on `/health`

### CAD
- [ ] Take measurements of ESP32-CAM board, battery pack, and microphone module
- [ ] Set up Fusion 360 project and shared workspace
- [ ] Research glasses frame reference designs for sizing

### Presentation / Documentation
- [ ] Create a shared slide deck (Google Slides / PowerPoint) with title slide and outline
- [ ] Document team member roles and contact info in the repo wiki or a `TEAM.md`
- [ ] Set up GitHub project board with columns (To Do, In Progress, Done)

---

## Week 2 — Core Building Blocks (Mar 16 – Mar 22)

### Electronics
- [ ] Write firmware to capture image on button press
- [ ] Write firmware to save captured image to microSD card
- [ ] Test microSD read/write reliability (write 20+ images, verify all readable)
- [ ] Wire up microphone module and test basic audio recording to SD

### Software
- [ ] Build face detection function — input image, output bounding boxes
- [ ] Build face encoding function — input cropped face, output encoding vector
- [ ] Build face comparison function — input two encodings, output match score
- [ ] Write unit tests for face detection (good image, no face, blurry image)
- [ ] Set up SQLite database schema for person profiles (name, notes, encoding, timestamps)

### CAD
- [ ] Design first draft of glasses frame (outer shell shape)
- [ ] Design camera mount that positions ESP32-CAM at eye level
- [ ] Identify where battery and wiring will route through the frame

### Presentation / Documentation
- [ ] Write "Problem Statement" and "Motivation" slides
- [ ] Write "System Architecture" slide with a block diagram
- [ ] Take photos/screenshots of hardware and software progress for slides

---

## Week 3 — Component Integration (Mar 23 – Mar 29)

### Electronics
- [ ] Add metadata logging — save timestamp + filename as JSON alongside each capture
- [ ] Implement capture status LED feedback (blink on success, double-blink on failure)
- [ ] Write firmware for Wi-Fi connection to a known local network
- [ ] Test Wi-Fi connection reliability from the ESP32-CAM

### Software
- [ ] Build sync API endpoint — `POST /upload` accepts image + metadata JSON
- [ ] Build import manager — saves uploaded files to organized folder structure
- [ ] Build duplicate detection — skip files that have already been imported
- [ ] Write database CRUD functions (create profile, update notes, list all, search by name)
- [ ] Write unit tests for sync API and database CRUD

### CAD
- [ ] Refine frame to fit all electronics (ESP32-CAM, mic, battery, button)
- [ ] Design button access point on the frame (reachable while wearing)
- [ ] 3D print first test version of the frame
- [ ] Test fit — check electronics fit, wearability, camera angle

### Presentation / Documentation
- [ ] Write "Hardware Design" slides with photos of wiring and frame
- [ ] Write "Software Pipeline" slides explaining capture → sync → detect → match flow
- [ ] Document any design decisions or trade-offs made this week

---

## Week 4 — End-to-End Connection (Mar 30 – Apr 5)

### Electronics
- [ ] Implement HTTP image upload from ESP32-CAM to laptop sync API
- [ ] Test full transfer: button press → capture → save to SD → upload via Wi-Fi
- [ ] Handle upload failures gracefully (retry logic, error LED indication)
- [ ] Optimize image resolution/compression for transfer speed vs. quality

### Software
- [ ] Build review UI page — display imported images in a grid
- [ ] Build enrollment flow — click a face, assign a name, save profile
- [ ] Build recognition flow — run new capture against enrolled profiles, show match
- [ ] Build profile view page — show person's name, notes, capture history
- [ ] Connect face pipeline to database (detection → encoding → match → profile lookup)

### CAD
- [ ] Iterate on frame based on test fit feedback
- [ ] Add microSD card access slot so card can be removed without disassembly
- [ ] Add on/off switch mount
- [ ] 3D print revised version

### Presentation / Documentation
- [ ] Write "Demo Walkthrough" slides describing the live demo steps
- [ ] Write "Privacy & Design Choices" slide (local-only processing, no cloud)
- [ ] Record a short video clip of the capture working (backup for live demo)

---

## Week 5 — Testing & Debugging (Apr 6 – Apr 12)

### Electronics
- [ ] Stress test: capture 50+ images, sync all, verify none lost
- [ ] Test battery life — how many captures before battery dies
- [ ] Fix any firmware bugs found during integration testing
- [ ] Clean up wiring — solder connections that were on breadboard

### Software
- [ ] Write integration tests — full capture-to-profile end-to-end test
- [ ] Test with varied face images (lighting, angles, glasses, masks)
- [ ] Handle edge cases: no face found, multiple faces, very low quality image
- [ ] Fix any bugs found during end-to-end testing
- [ ] Optimize face matching speed if slow (< 2 seconds per match target)

### CAD
- [ ] Final fit test with soldered electronics (not breadboard)
- [ ] Fix any comfort or durability issues
- [ ] Print final version of the shell
- [ ] Paint or finish the shell if desired

### Presentation / Documentation
- [ ] Write "Testing & Results" slides with metrics (match accuracy, sync speed, battery life)
- [ ] Write "Challenges & Lessons Learned" slide
- [ ] Finalize slide deck structure and flow

---

## Week 6 — Polish & Demo Prep (Apr 13 – Apr 19)

### Electronics
- [ ] Final firmware cleanup — remove debug prints, ensure stable boot
- [ ] Verify the device works reliably for a 10-minute demo window
- [ ] Prepare a backup SD card with pre-loaded test images (demo safety net)
- [ ] Charge batteries, label cables, organize hardware kit

### Software
- [ ] Polish UI — clean layout, clear labels, no broken pages
- [ ] Pre-load database with 3–5 enrolled demo profiles for the presentation
- [ ] Add any helpful UI touches (loading spinner, success/error messages)
- [ ] Final test coverage check — run `pytest --cov` and fill critical gaps

### CAD
- [ ] Ensure final printed shell is clean and presentable
- [ ] Attach all electronics securely inside the shell
- [ ] Take final photos of the assembled prototype for slides

### Presentation / Documentation
- [ ] Finalize all slides — content, visuals, formatting
- [ ] Write speaker notes for each slide
- [ ] Plan who presents which section (assign among 5 members)
- [ ] Do at least one full rehearsal run-through

---

## Week 7 — Final Demo & Presentation (Apr 20 – Apr 26)

### Electronics
- [ ] Day-of hardware check: power on, capture, sync — all working
- [ ] Bring backup battery and backup ESP32-CAM if available

### Software
- [ ] Day-of software check: start server, verify UI loads, test one recognition
- [ ] Have backup screenshots/video ready in case of live demo failure

### CAD
- [ ] Bring assembled prototype ready to show and pass around

### Presentation / Documentation
- [ ] Deliver the presentation
- [ ] Run the live demo
- [ ] Collect any feedback from audience / instructor
- [ ] Do a team retrospective — what went well, what would we change

---

## Week 8 — Buffer / Submission (Apr 27 – Apr 30)

### All Categories
- [ ] Address any feedback received after the presentation
- [ ] Clean up repo: remove dead code, update README, ensure docs are current
- [ ] Write a short final report or project summary if required
- [ ] Submit all deliverables by the deadline
- [ ] Celebrate

---

## Task Summary by Category

| Category | Total Tasks |
|----------|-------------|
| Electronics | 27 |
| Software | 28 |
| CAD | 17 |
| Presentation / Documentation | 19 |
| **Total** | **91** |

Each team member should aim for roughly **18 tasks** across the project, but this will vary based on task complexity. Pick what matches your skills and interests, and coordinate with the team so nothing gets dropped.
