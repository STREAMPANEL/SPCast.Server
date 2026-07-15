# FFmpeg Dependency Guide

The SPCast Media Edge server is designed as a standalone, high-performance streaming server. To ensure maximum compatibility with different hardware acceleration architectures (e.g., NVENC, Intel QuickSync) and due to varying licensing landscapes, **the FFmpeg multimedia framework is not pre-bundled with the server.**

## When is FFmpeg Required?
You only need to install or provide FFmpeg if you intend to use any of the advanced media manipulation features of the SPCast server.

**These include**
- Ingesting and transcoding video/audio streams into different qualities or resolutions.
- Re-broadcasting or pushing streams to external platforms (e.g., YouTube, Twitch) using custom encoder settings.
- Advanced protocol bridging where raw audio/video data needs to be altered.

If you are using the server purely for standard pass-through streaming, direct playback, or native HTTP routing, the core server will function perfectly without FFmpeg.

## Installation & Setup
It is your responsibility as the server administrator to download and provide a compatible FFmpeg executable for your specific operating system and architecture.

You must ensure that the `ffmpeg` executable is either placed locally in the same directory as the SPCast server, or is globally accessible via your system's `PATH` variable.

### Official Download Sources

* **macOS:** [https://evermeet.cx/ffmpeg/](https://evermeet.cx/ffmpeg/)
  *(Alternatively via Homebrew: `brew install ffmpeg`)*
* **Windows:** [https://www.gyan.dev/ffmpeg/builds/](https://www.gyan.dev/ffmpeg/builds/)
  *(Download the "essentials" or "full" static build and extract the `.exe`)*
* **Linux:** [https://www.ffmpeg.org/download.html](https://www.ffmpeg.org/download.html)
  *(Alternatively via package manager: `sudo apt install ffmpeg` or `dnf install ffmpeg`)*

> **Note:** Always ensure you download the static build matching your machine's CPU architecture (`x86_64`, `aarch64`/`ARM`) to prevent execution failures.