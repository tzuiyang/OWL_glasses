# Test Coverage Analysis & Proposal

## Current State

**Test coverage: 0%** — The repository has no source code or tests yet. This analysis is based on the planned architecture in `workflow.md` and proposes a testing strategy to adopt as development begins.

---

## Recommended Testing Strategy by Component

### 1. Firmware (ESP32-CAM) — `firmware/`

Embedded code is hardest to test but benefits enormously from it. Target areas:

| Area | What to Test | Suggested Approach |
|---|---|---|
| **Image capture logic** | Camera init, capture trigger on button press, correct JPEG encoding | Unit tests with mocked camera HAL; hardware-in-the-loop (HIL) smoke tests |
| **SD card file I/O** | Write image, write audio, write metadata JSON, handle full card gracefully | Unit tests with a mock filesystem; integration tests on real hardware |
| **Metadata generation** | Correct timestamp format, unique file names, proper JSON structure | Pure unit tests (no hardware dependency) |
| **Wi-Fi sync transfer** | HTTP upload endpoint responds, files transfer completely, partial transfer recovery | Integration tests against a mock server; end-to-end with real laptop |
| **Button debounce / input** | Single press = single capture, no double-fire, held button behavior | Unit tests with simulated GPIO timing |

**Priority: High** — Metadata generation and SD card I/O are pure logic that can be tested without hardware. Start here.

---

### 2. Laptop Sync Service — `software/` (FastAPI/Flask)

This is the data bridge between device and processing pipeline.

| Area | What to Test | Suggested Approach |
|---|---|---|
| **File upload endpoint** | Accepts valid image+audio, rejects corrupt/oversized files, returns proper status codes | `pytest` + FastAPI `TestClient` or Flask test client |
| **Duplicate detection** | Same file uploaded twice is handled (skip or overwrite based on policy) | Unit tests with fixture files |
| **Import folder management** | Files land in correct directory structure, filenames are sanitized | Unit tests |
| **Sync state tracking** | Tracks which files have been imported, avoids re-importing | Unit tests against SQLite/JSON store |
| **Error handling** | Network interruption mid-transfer, malformed metadata, disk full | Unit tests with mocked I/O failures |

**Priority: High** — The sync API is a critical integration point. Test it early and thoroughly.

---

### 3. Face Processing Pipeline — `software/`

This is the core value of the product.

| Area | What to Test | Suggested Approach |
|---|---|---|
| **Face detection** | Detects faces in good images, returns empty for no-face images, handles blurry/dark images | Unit tests with a curated fixture image set |
| **Face encoding** | Produces consistent encodings for the same person, different encodings for different people | Unit tests with known face pairs |
| **Face matching / comparison** | Correct match above threshold, no false match below threshold, behavior at boundary | Unit tests with encoding fixtures; parameterized threshold tests |
| **Multi-face handling** | Image with 2+ faces returns all detections | Unit tests with group photo fixtures |
| **Image preprocessing** | Resize, rotation correction, color normalization produce expected output | Unit tests comparing output to golden reference images |

**Priority: Critical** — This pipeline determines whether the product works. Invest in a solid fixture set of test images (varied lighting, angles, occlusions).

---

### 4. Local Database (People/Profiles) — `software/`

| Area | What to Test | Suggested Approach |
|---|---|---|
| **CRUD operations** | Create, read, update, delete person profiles | Unit tests against an in-memory SQLite DB |
| **Schema integrity** | Required fields enforced, foreign key constraints hold | Migration tests, schema validation tests |
| **Search / lookup** | Find person by name, find by face encoding match | Unit tests |
| **Data export / backup** | Export produces valid JSON/CSV, import restores correctly | Round-trip tests |
| **Concurrent access** | Two operations don't corrupt data | Stress tests with threading |

**Priority: Medium** — Standard CRUD testing, but important for data integrity.

---

### 5. User Interface (Streamlit / Web UI) — `software/`

| Area | What to Test | Suggested Approach |
|---|---|---|
| **Sync page** | Trigger sync, display progress, show errors | Selenium/Playwright end-to-end tests or Streamlit's `AppTest` |
| **Review page** | Display captured images, allow labeling, save correctly | End-to-end tests |
| **Profile page** | View person details, edit notes, see capture history | End-to-end tests |
| **Input validation** | Empty names rejected, overly long notes handled | Unit tests on form validators |

**Priority: Low** — UI tests are valuable but should come after backend logic is solid.

---

### 6. End-to-End Integration Tests

| Scenario | What to Verify |
|---|---|
| **Full capture-to-profile flow** | Button press → image saved → synced to laptop → face detected → profile created |
| **Recognition after enrollment** | Enroll person A → capture new image of A → system returns correct match |
| **Unknown face handling** | Capture image of unenrolled person → system flags as unknown → user can enroll |
| **Offline resilience** | Capture 50 images offline → sync all at once → all processed correctly |

**Priority: High** — These validate the product promise end-to-end.

---

## Recommended Test Tooling

| Tool | Purpose |
|---|---|
| `pytest` | Python test runner for all laptop-side tests |
| `pytest-cov` | Coverage measurement and reporting |
| `FastAPI TestClient` / `Flask test client` | HTTP endpoint testing without a running server |
| `unittest.mock` / `pytest-mock` | Mocking external dependencies (camera, filesystem, network) |
| `PlatformIO` test framework | Firmware unit tests (runs on host or device) |
| `Playwright` or `Streamlit AppTest` | UI end-to-end tests |
| `factory_boy` or fixtures | Consistent test data generation |

---

## Proposed Test Directory Structure

```text
firmware/
  test/
    test_metadata.cpp        # Metadata generation unit tests
    test_sd_io.cpp            # SD card I/O tests (mocked)
    test_capture_logic.cpp    # Capture flow tests

software/
  tests/
    conftest.py               # Shared fixtures (test images, DB setup)
    test_sync_api.py          # Sync endpoint tests
    test_face_detection.py    # Face detection pipeline tests
    test_face_matching.py     # Face comparison/matching tests
    test_database.py          # Profile CRUD tests
    test_image_processing.py  # Preprocessing tests
    test_integration.py       # End-to-end workflow tests
    fixtures/
      images/                 # Test face images (varied conditions)
      audio/                  # Test audio clips
      metadata/               # Sample metadata JSON files
```

---

## Top 5 Areas to Prioritize

1. **Face detection & matching pipeline** — This is the core product logic. False positives/negatives directly impact usability. Build a diverse test image fixture set early.

2. **Sync API endpoints** — The device-to-laptop bridge must be rock solid. Test uploads, error cases, and duplicate handling thoroughly.

3. **Metadata generation (firmware)** — This is pure logic that can be unit tested without hardware. Incorrect metadata corrupts the entire downstream pipeline.

4. **Database CRUD operations** — Data integrity is essential. Test with in-memory SQLite to keep tests fast.

5. **End-to-end capture-to-recognition flow** — Validates that all components work together. Even a single happy-path integration test catches interface mismatches early.

---

## CI Recommendations

Once tests exist, add a GitHub Actions workflow that:
- Runs `pytest` on every PR
- Reports coverage with `pytest-cov`
- Fails the build if coverage drops below a threshold (start at 60%, raise over time)
- Runs PlatformIO firmware tests on host (no hardware needed for unit tests)
