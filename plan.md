# OWL Glasses Prototype Plan

## Is This Doable?

Yes, this is doable as a proof-of-concept.

What is realistic for a student prototype:
- An `ESP32-CAM` captures a face image.
- The device sends that image to a local laptop over Wi-Fi.
- The laptop runs local face matching against a small enrolled database.
- The laptop shows or speaks back the matched person's name and a few notes.
- A `Fusion 360` shell makes the hardware look like glasses for demo purposes.

What is not necessary for the first prototype:
- True AR display inside the lens
- Full-day battery life
- Production-grade facial recognition
- Fully hidden electronics
- Cloud backend
- Mobile app

The project becomes hard only if you try to build too much at once. The first version should prove only one core idea:

`Can a wearable-looking device capture a face and retrieve saved identity/context locally?`

## Prototype Goal

Build a demo where:
1. A teammate presses a button on the glasses prototype.
2. The `ESP32-CAM` takes a photo.
3. The photo is sent to a local laptop.
4. The laptop compares the face to a small local database.
5. The laptop returns the best match and shows:
   - name
   - confidence
   - 1 to 3 personal/professional notes
6. Optionally, the laptop speaks the result through headphones or a speaker.

## Team Structure

Recommended split:

- `Hardware lead`
  - ESP32-CAM
  - wiring
  - power
  - button input
  - camera capture and image sending

- `Software lead`
  - local server
  - face recognition
  - database
  - recognition pipeline

- `CAD/fabrication lead`
  - Fusion 360 shell
  - camera placement
  - electronics fit
  - print-ready files

- `Integration/demo lead`
  - combines hardware and software
  - testing
  - slides
  - demo script

If your team is small, one person can cover two roles.

## System Architecture

### Hardware

- `ESP32-CAM`
- 1 pushbutton
- portable power source
- simple glasses-like frame or printed shell

Optional:
- second button for enroll mode
- LED indicator
- small speaker or earbud path

### Software

- Laptop runs local Python app
- Local database of known people
- Face recognition library
- UI for enrollment and recognition results

### Data Flow

1. User presses capture button.
2. ESP32-CAM captures JPEG.
3. ESP32-CAM sends image by HTTP over local Wi-Fi.
4. Laptop server receives image.
5. Recognition script finds the closest face match.
6. Laptop returns JSON result.
7. UI displays the result or reads it aloud.

## Recommended Tech Stack

### Embedded

- `ESP32-CAM`
- Arduino IDE or PlatformIO
- Wi-Fi + HTTP POST

### Local App

- `Python`
- `Flask` or `FastAPI` for local API
- `Streamlit` for quick UI, if you want a simple demo interface
- `OpenCV` for image handling
- `face_recognition` if installable, otherwise `DeepFace`
- `SQLite` or `JSON` for profile storage
- `pyttsx3` or another offline text-to-speech tool for optional audio output

### CAD

- `Fusion 360`
- 3D print or foamboard/mock shell for early iterations

## Scope Control

To stay on track, define phases clearly.

### Must Have

- Capture image from ESP32-CAM
- Send image to laptop
- Local face match from a small test database
- Display result on laptop
- Wearable-looking shell

### Nice to Have

- Audio response
- Enroll new person from the app
- Better shell fit and cable routing
- Confidence score
- Multiple saved notes per person

### Do Not Build in V1

- Lens display
- Real-time continuous recognition
- Cloud sync
- Full privacy/legal framework
- Advanced microphone pipeline
- On-device face recognition on the ESP32

## Baby-Step Build Plan

## Phase 1: Project Setup

### Step 1. Create the repo

- Create a GitHub repo for the team.
- Add folders:
  - `firmware/`
  - `software/`
  - `cad/`
  - `docs/`
  - `demo_assets/`
- Add:
  - `README.md`
  - `plan.md`
  - `team_roles.md`
  - `parts_list.md`

### Step 2. Define success criteria

Write one short statement in the repo:

`Our prototype succeeds if we can capture a face using a wearable-mounted ESP32-CAM and locally return a correct profile match on a laptop.`

### Step 3. Buy or gather parts

- ESP32-CAM
- USB programmer for ESP32-CAM if needed
- pushbutton
- jumper wires
- battery pack or USB power bank
- cheap glasses frame or mock frame
- access to 3D printer

### Step 4. Assign ownership

Each task should have one owner and one backup.

## Phase 2: ESP32-CAM Bring-Up

### Step 5. Verify camera works

- Flash a basic camera example to the ESP32-CAM.
- Confirm it captures still images.
- Confirm image quality is good enough for face detection.

### Step 6. Add Wi-Fi connectivity

- Connect ESP32-CAM to local Wi-Fi.
- Print local IP address to serial monitor.
- Verify stable connection.

### Step 7. Send a test image to laptop

- Create a very simple local server on laptop that receives an uploaded image.
- From ESP32-CAM, send one captured JPEG using HTTP POST.
- Save uploaded image on laptop.
- Confirm image transfer works repeatedly.

### Step 8. Add button trigger

- Wire a pushbutton.
- Press button to trigger photo capture and upload.
- Add LED blink or serial logs for status:
  - button pressed
  - photo taken
  - upload success
  - upload failed

## Phase 3: Local Recognition Software

### Step 9. Build the local server

- Create a Python app with endpoints:
  - `/upload`
  - `/recognize`
  - optional `/enroll`
- Save uploaded images into a local folder.

### Step 10. Build a tiny people database

Start with 5 to 10 people max.

For each person store:
- name
- major/job
- where you met
- one memorable note
- profile image(s)

### Step 11. Implement face detection and matching

- Detect face in uploaded image.
- Compare with enrolled faces.
- Return:
  - best match name
  - confidence or distance
  - notes from local database

### Step 12. Create a simple UI

Minimal UI pages:
- `Enroll person`
- `Recognize person`
- `People database`

The UI can be ugly. It only needs to be functional.

### Step 13. Test recognition accuracy

Run tests with:
- front-facing images
- slightly angled faces
- different lighting
- glasses on/off if relevant

Document failure cases.

## Phase 4: CAD Shell and Wearable Mockup

### Step 14. Measure components

Measure:
- ESP32-CAM board
- lens opening needed for camera
- button size
- battery dimensions
- wire paths

### Step 15. Create rough CAD shell in Fusion 360

First shell goal:
- hold camera
- look roughly like glasses
- allow access to button and power
- do not hide everything perfectly

This first shell should be fast and ugly if needed.

### Step 16. Print low-fidelity prototype

- Use low quality print settings to save time.
- Check fit, board placement, and camera angle.
- Mark where shell blocks the camera or makes wearing impossible.

### Step 17. Revise shell

- improve camera alignment
- reduce bulk where possible
- add mounting points or tape channels
- improve comfort just enough for demo use

## Phase 5: Integration

### Step 18. Mount hardware in shell

- Secure ESP32-CAM in shell.
- Route wires safely.
- Verify camera is unobstructed.
- Verify button remains accessible.

### Step 19. Full end-to-end test

Run the exact intended demo flow:
1. Wear prototype
2. Press button
3. Photo uploads
4. Laptop recognizes face
5. Result appears on screen

Repeat until stable.

### Step 20. Add optional audio output

If time allows:
- laptop reads back: `This looks like Kevin. Met at NYU. Interested in startups.`

## Phase 6: Demo Preparation

### Step 21. Build a controlled demo dataset

Use only teammates or volunteers who consent.

For each demo person:
- take 2 to 4 enrollment photos
- verify recognition in the same room as the demo

### Step 22. Prepare a fallback demo mode

If live recognition fails:
- use saved test photos
- show the local processing pipeline
- show shell and hardware separately

### Step 23. Prepare slides

Slides should show:
- problem
- concept
- architecture
- privacy angle
- prototype photos
- what worked
- what did not
- next steps

## Suggested Repo Structure

```text
innovation_project/
  firmware/
    esp32_cam_sender/
  software/
    app/
    data/
    tests/
    requirements.txt
  cad/
    fusion/
    exports/
  docs/
    architecture.md
    test_log.md
    meeting_notes.md
  demo_assets/
  README.md
  plan.md
  team_roles.md
  parts_list.md
```

## 6-Week Schedule

This schedule assumes a class project pace and a team of 3 to 5 people.

## Week 1: Lock scope and set up repo

Goals:
- confirm the prototype scope
- assign owners
- create repo
- order or gather parts

Tasks:
- create GitHub repo
- create folder structure
- upload planning docs
- decide what counts as success
- assign hardware/software/CAD leads
- list all parts needed

Deliverables:
- repo created
- plan approved
- parts list complete
- team roles assigned

## Week 2: Bring up ESP32-CAM and local server

Goals:
- make camera work
- connect to Wi-Fi
- send one image to laptop

Tasks:
- flash ESP32-CAM camera example
- connect board to local Wi-Fi
- create simple Python server
- upload one JPEG successfully
- save image locally on laptop

Deliverables:
- photo captured from hardware
- image uploaded to laptop over Wi-Fi

## Week 3: Build face recognition MVP

Goals:
- local recognition works on a small dataset

Tasks:
- choose recognition library
- create 5 to 10 person dataset
- build basic local database
- return match name and notes
- create simple UI

Deliverables:
- local app recognizes at least a few enrolled people
- result is visible on laptop

## Week 4: CAD shell V1

Goals:
- create wearable-looking prototype shell

Tasks:
- measure ESP32-CAM and button layout
- model shell in Fusion 360
- print rough prototype
- test camera angle and fit
- revise CAD

Deliverables:
- shell V1 printed
- camera can be mounted in wearable form

## Week 5: Integration and debugging

Goals:
- complete end-to-end wearable demo

Tasks:
- mount hardware in shell
- connect button trigger
- run repeated capture and recognition tests
- improve reliability
- add optional text-to-speech if time allows

Deliverables:
- one-button end-to-end demo works

## Week 6: Demo polish and contingency prep

Goals:
- make the presentation stable
- reduce risk of failure during demo

Tasks:
- test with real demo users
- record backup video
- write demo script
- build presentation slides
- document limitations and future work

Deliverables:
- stable live demo
- fallback video/demo mode
- final presentation assets

## Suggested Weekly Meeting Pattern

### One full-team meeting each week

Agenda:
- what was completed
- blockers
- next tasks
- risks

### Two short async check-ins each week

Each person posts:
- what they did
- what is blocked
- what they will do next

## Risks and Mitigations

### Risk 1. Face recognition is unreliable

Mitigation:
- keep dataset small
- use controlled lighting
- use front-facing enrollment photos
- demo with known teammates

### Risk 2. ESP32-CAM image quality is poor

Mitigation:
- lower scope to still images only
- improve lighting
- bring subject closer
- test multiple camera settings

### Risk 3. CAD shell takes too long

Mitigation:
- use an ugly shell first
- tape/zip-tie mounting is acceptable for early demo
- print low fidelity before polishing

### Risk 4. Wi-Fi communication breaks during demo

Mitigation:
- use a dedicated hotspot or known local network
- prepare backup saved-image demo
- test in the demo room beforehand

### Risk 5. Team work becomes fragmented

Mitigation:
- keep repo organized
- assign one owner per task
- use short weekly checkpoints

## Definition of Done

The prototype is done when:
- the `ESP32-CAM` captures a face photo from the wearable shell
- the image is sent to a local laptop
- the laptop recognizes the person from a small enrolled database
- the system displays that person's name and notes
- the team can demonstrate the full flow reliably

## Immediate Next 5 Actions

1. Create the GitHub repo and folder structure.
2. Order or gather the `ESP32-CAM`, programmer, button, and power parts.
3. Flash a basic camera example and confirm photo capture.
4. Build a tiny Python server that receives uploaded images.
5. Create a 5-person local test dataset for recognition experiments.
