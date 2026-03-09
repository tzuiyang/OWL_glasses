# OWL Glasses Workflow

## Purpose

This document defines the planned workflow for the OWL Glasses proof-of-concept.

It does not describe a finished implementation. Its purpose is to give the team a shared reference for:
- what the system is supposed to do
- what hardware is needed
- what software is needed
- how data moves through the system
- what should happen online and offline

The prototype goal is to prove that a wearable-looking device can capture face images, store them locally when needed, and later sync them to a local laptop for private processing.

## Core Prototype Idea

The first prototype is based on `offline capture` and `later local sync`.

That means:
- the glasses device does not need constant Wi-Fi
- the wearable captures images and short audio while out in the real world
- the device stores those captures on a `microSD` card
- later, when the user is back near a trusted local laptop and Wi-Fi network, the stored data is transferred
- the laptop processes the data locally and manages the face/profile database

This avoids trying to run heavy recognition directly on the `ESP32-CAM`.

## High-Level System Overview

The system has three major parts:

1. `Wearable capture device`
- glasses-like shell
- `ESP32-CAM`
- button input
- power source
- `microSD` storage

2. `Local laptop software`
- sync interface
- face processing pipeline
- local database of people and notes
- simple user interface for review and enrollment

3. `CAD shell`
- a wearable housing designed in `Fusion 360`
- holds the electronics in a glasses-like form for demonstration

## Main Operating Modes

The prototype should be thought of as having three modes.

### 1. Capture Mode

Used when the wearer is outside, mobile, or not connected to Wi-Fi.

In this mode:
- the user presses a button
- the `ESP32-CAM` captures an image
- a microphone also records a short audio clip
- the image and audio are saved to `microSD`
- metadata is saved alongside the capture

Possible metadata:
- timestamp
- file name
- linked audio file name
- capture mode
- device ID

The important point is that the system should still work without internet and without a nearby computer.

### 2. Sync Mode

Used later when the user returns to a trusted environment.

In this mode:
- the device connects to local Wi-Fi
- the laptop discovers or connects to the device
- stored images are uploaded or downloaded
- the laptop saves imported files locally
- the device marks those files as synced, or the laptop keeps track of what has already been imported

This mode transfers data from the wearable to the laptop without using cloud services.

### 3. Review / Processing Mode

Used on the laptop after sync.

In this mode:
- the laptop runs face detection and matching
- the user reviews imported captures
- the user labels people or connects a face to an existing person profile
- the system stores notes and context locally

This is where the actual memory-assist value gets created.

## End-to-End Workflow

## Phase A: Before Use

1. Charge the device.
2. Insert and verify the `microSD` card.
3. Confirm the camera module is functioning.
4. Confirm the shell is assembled and wearable enough for testing.
5. Confirm the laptop software is ready to receive and process synced files later.

## Phase B: During Capture

1. User wears the prototype.
2. User presses the capture button when they want to record a face or interaction.
3. `ESP32-CAM` captures a still image.
4. A microphone records a short audio clip tied to that capture event.
5. The image and audio are stored locally on `microSD`.
6. A simple status indicator confirms success or failure.

Possible future status indicators:
- LED blink
- short buzzer
- serial debug output during development

## Phase C: After Returning to Local Workspace

1. Device powers on in sync mode.
2. Device connects to a trusted local Wi-Fi network.
3. Laptop software opens sync session.
4. Stored captures are transferred from the device to the laptop.
5. Laptop stores the raw files in a local import folder.

## Phase D: Local Processing

1. Laptop scans imported captures.
2. Face detection identifies usable face regions.
3. Recognition pipeline compares detected faces against enrolled profiles.
4. User reviews results.
5. User confirms or corrects identity.
6. Local database is updated with:
   - person identity
   - notes
   - profile image references
   - capture history

## Phase E: Future Recognition Demo

After enough people are enrolled:
- a later version of the prototype can attempt recognition after sync
- or a live local Wi-Fi demo can send a fresh image from the glasses to the laptop and return a match

This should be considered a later-stage demonstration, not the first requirement.

## Hardware Plan

## Core Hardware

- `ESP32-CAM`
- `microSD` card
- microphone module
- pushbutton
- power source or battery pack
- wiring and connectors
- glasses frame or 3D printed shell

## Likely Supporting Hardware

- USB-to-serial programmer for flashing the board
- breadboard or soldered prototype board
- status LED
- on/off switch
- mounting hardware

## Optional Hardware

- second button for alternate mode
- buzzer for feedback
- external battery holder
- removable storage access hatch in shell

## Hardware Responsibilities

The wearable hardware is responsible for:
- capturing images
- capturing short audio clips
- storing data locally
- connecting later for sync
- surviving enough handling for a demo

The hardware is not responsible for:
- heavy face recognition
- database management
- cloud sync
- advanced UI

## Software Plan

## Device-Side Software

The `ESP32-CAM` firmware should eventually handle:
- camera initialization
- microphone input
- button input
- image capture
- audio capture
- file save to `microSD`
- metadata logging
- later sync over local Wi-Fi

The device firmware should stay simple.

## Laptop-Side Software

The laptop software should eventually handle:
- importing captured files
- managing synced/not-synced state
- face detection
- face comparison
- local people database
- user review interface
- optional text-to-speech

## Suggested Software Components

### Embedded side

- Arduino IDE or PlatformIO project
- `ESP32-CAM` camera libraries
- SD card file handling
- local Wi-Fi transfer logic

### Laptop side

- `Python`
- `FastAPI` or `Flask` for a local sync API
- `Streamlit` or a simple local web UI for review
- `OpenCV` for image handling
- `face_recognition` or similar library for local matching
- `SQLite` or `JSON` for local storage

## Data Plan

The system will likely store two kinds of data.

### 1. Raw capture data

Stored first on the `microSD` card, later copied to laptop.

Examples:
- captured image files
- captured audio files
- capture timestamps
- file identifiers

### 2. Processed profile data

Stored on the laptop only.

Examples:
- person name
- where met
- role / major / context
- personal notes
- profile images
- recognition history

The laptop should be treated as the main long-term database location.

## Storage Plan

### On the wearable

Store only what the device needs:
- captured images
- captured audio clips
- simple metadata
- temporary sync state

### On the laptop

Store the long-term local project data:
- enrolled people
- notes
- recognition results
- imported raw images

This supports the privacy-first design goal because the main data stays local to the user.

## Connectivity Plan

The prototype should support two connectivity assumptions:

### Offline use

- no Wi-Fi available
- still able to capture and save images

### Later local sync

- connect only to a trusted local network
- transfer files to a local laptop
- no cloud dependency

Internet access should not be required for the prototype workflow.

## User Interaction Plan

For the first prototype, user interaction should stay extremely simple.

### On the wearable

- one button to capture
- optional indicator for successful save

### On the laptop

- sync button or sync page
- review captures
- create or update person profiles
- view matched people and notes

The system should prioritize clarity over sophistication.

## Demo Workflow

A realistic class demo could look like this:

1. Show the wearable shell with mounted `ESP32-CAM`.
2. Press button and capture several example image-and-audio samples.
3. Explain that the images and short conversation clips are being stored locally on `microSD`.
4. Return to laptop and start sync mode.
5. Import captures from the device.
6. Run local review and recognition flow.
7. Show how a person can be matched or labeled with notes, with audio available as supporting context.
8. Explain how this supports memory assistance while preserving privacy.

## What This Prototype Is Proving

The prototype is meant to prove:
- a wearable form factor is possible
- image capture from a glasses-like device is possible
- offline collection is possible
- later local sync is possible
- local processing and memory-assist workflow are possible

It is not meant to prove:
- perfect facial recognition accuracy
- real-time AR overlay
- polished industrial design
- production-ready privacy compliance

## Scope Limits

To keep the project realistic, the team should avoid expanding scope too early.

Do not treat these as first-version requirements:
- live streaming camera feed at all times
- on-device recognition on the `ESP32`
- cloud database
- full mobile app
- hidden miniature optics display
- advanced voice assistant features

## Team Use of This Document

This document should be used to:
- align on the architecture before building
- split hardware/software/CAD responsibilities
- reduce confusion about online vs offline behavior
- keep the prototype within scope

It should be updated as the team makes real technical decisions, but it should stay focused on workflow rather than low-level implementation details.
