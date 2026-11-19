# RISY FaceLog

Real-time, GPU-accelerated face recognition built into an **attendance system**. Employees look at a camera and their attendance is logged automatically by face.

Built by A Abdul Hadi. See [LICENSE](LICENSE): all rights reserved.

## What it does

- **Live recognition** from a webcam, with names and status drawn over the video.
- **Enrolment** of new people from five head poses, with quality checks on every sample.
- **Anti-spoofing**: tries to tell a real face from a photo or screen, and asks the person to turn their head when unsure.
- **Attendance log** with an activity page.
- **Web interface** with Live, Enroll, People, Activity and Settings pages.
- **Admin sign-in** protecting the management pages.
- **Face data encrypted at rest** (AES-256-GCM).
- **Erase a person permanently** from the People page.

## How recognition works

1. SCRFD-10G detects faces and five landmarks.
2. The face is aligned to 112x112.
3. ArcFace R50 produces a 512-dimensional embedding, L2-normalised.
4. The embedding is matched to the enrolled gallery by cosine similarity (a dot product), with a vote over several frames before attendance is logged.

Liveness uses two MiniFASNet models plus a model-free 3-D head-movement test, and skips poor frames (blurred, moving, turned, badly lit) instead of failing them.

## Tech stack

- **Engine:** Python, ONNX Runtime (CUDA), OpenCV, NumPy
- **Server:** FastAPI
- **Frontend:** React (Vite)
- **Tested on:** Windows 11, NVIDIA RTX 3050 (6 GB), Python 3.13

## Run

1. Install Python dependencies: `pip install -r requirements.txt`
2. Put the required `.onnx` model files in `models/` (not included).
3. Build the UI: `npm --prefix web/frontend run build`
4. Start the server (port 8766) and open `http://localhost:8766`. On Windows, `RISY FaceLog.bat` does this.

There is also a bare OpenCV mode: `python main.py live`.

## Project layout

- `facerec/` recognition engine, enrolment, liveness, camera, settings
- `web/server/` FastAPI app, camera thread, attendance log
- `web/frontend/` React interface
- `docs/reference.md` detailed technical notes and tuning

## Privacy

Face images and templates are biometric data. They are stored in `data/`, encrypted, and are never committed to this repository. Deploying this for real people requires their consent and compliance with local law.

## Status

Working prototype. Tested by the author against phone photos, phone video and prints, in different lighting, and with a larger employee database.

The optional second-opinion anti-spoof model is trained on a non-commercial dataset, so check its licence before any commercial use.
