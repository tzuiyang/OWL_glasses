# OWL Glasses

OWL Glasses is a student prototype for privacy-first smart glasses that help users remember people by capturing a face image, matching it against a local database, and returning stored context such as name and notes.

## Prototype Goal

The proof of concept focuses on one core workflow:

1. Capture a face image with an `ESP32-CAM`.
2. Send the image to a local device over Wi-Fi.
3. Match the face locally on a laptop.
4. Return the person's name and saved notes.

## Project Structure

```text
firmware/
software/
cad/
docs/
demo_assets/
```

## Current Scope

In scope for the first prototype:
- `ESP32-CAM` image capture
- local image upload to laptop
- local face matching
- simple profile database
- glasses-like shell designed in `Fusion 360`

Out of scope for the first prototype:
- true AR display
- cloud backend
- production-ready industrial design
- full mobile app

## Team Workflow

- Work on feature branches, not `main`
- Open a pull request for every change
- Keep pull requests focused and small when possible
- Use issues or project notes to track ownership and blockers

## Main Planning Doc

See [plan.md](./plan.md) for the detailed build plan, baby-step tasks, and schedule.
