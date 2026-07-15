# SPCast Media Edge - Main Architecture & Overview

Welcome to the central documentation for **SPCast Media Edge**. This document provides a high-level overview of the server's architecture, core components, and how the different systems interact.

## 1. Architectural Philosophy

SPCast Media Edge is a high-performance, modern streaming edge server built entirely from the ground up in **100% pure Rust**. 
It is engineered for extreme efficiency designed to run close to the metal with near-zero CPU footprint on idle and minimal resource utilization even under heavy load.

- **Zero System Dependencies:** The core routing and connection handling operates completely stand-alone. No external web servers (like Nginx/Apache) or background runtimes are strictly required for standard operations.
- **Zero-CPU Passthrough:** When receiving audio formats like MP3 or AAC directly from a broadcaster, the server securely routes the packets directly to connected listeners without transcoding. This consumes virtually no CPU.
- **Event-Driven & Asynchronous:** Built heavily on the Tokio runtime, ensuring that the server never blocks or "busy-waits", saving massive amounts of energy and CPU cycles.

## 2. Core Subsystems

The server is cleanly separated into distinct operational subsystems.

### 2.1 Ingest (Input)
The Ingest layer is responsible for receiving live streams from broadcasters (e.g. OBS Studio, RadioBOSS, SAM Broadcaster and so on.). 
- **Icecast & Shoutcast:** Emulates legacy directory servers to accept incoming streams from classic audio broadcasting software.
- **SRT (Secure Reliable Transport):** A modern, resilient protocol ideal for unstable connections (like 4G/LTE mobile streams) and video broadcasts.
- **HTTP Pull:** Fetches and relays existing streams from external remote servers.

### 2.2 Segmenter & Pipeline
Once data enters the server, it is passed into the Pipeline. 
- **Memory-Ring Store:** HLS and DASH streams are segmented into small chunks and kept entirely in RAM using a highly efficient Memory-Ring Store. Once segments age out of the live window, they are safely discarded or sent to the DVR Spool.
- **Transcoding (via FFmpeg):** If the server needs to actively alter the format of the stream (e.g., converting an AAC stream to OPUS, or processing Video DASH), the server natively spawns and manages an FFmpeg process. *See [ffmpeg.md](ffmpeg.md) for details.*

### 2.3 Delivery (Output)
The Delivery engine is the HTTP frontend serving data to viewers.
- Native TLS/SSL support.
- Fully automated CORS-Header injection for browser-based playback.
- **Audio Stream Mounts:** Direct `live.mp3`, `live.aac`, or `live.opus` connections for classic media players or radio directories.
- **Segmented Delivery:** Serving `.m3u8` playlists, `.mpd` manifests, and raw segment chunks (`.m4s`, `.mp4`).

## 3. Configuration

The server relies entirely on an XML-based configuration model.
There is **no database** required for configuration.

- **Primary File:** The entire server configuration is loaded from `config.xml`.
- **Reference:** For a complete list of all available configuration blocks, tags, and parameters, please review the heavily commented `config/full_config_example.xml` file or simply `config/monimal_config_example.xml`.
- **Hot-Reloading:** Many configuration changes (like ACLs or IP limitations) can be reloaded at runtime via the Admin API without interrupting active listener sessions.

## 4. Documentation Index

To explore the specific features of SPCast Media Edge, please refer to the dedicated documentation files:

- **[Installation Guide](installation.md)** - Requirements and how to run the binary.
- **[Playable Links](playable_links.md)** - How broadcasters connect and how listeners tune in.
- **[API & Admin Endpoints](api.md)** - Internal control API, Prometheus metrics, and Server-Sent Events.
- **[Player Documentation](player.md)** - Details about the built-in HTML5 Web Player.
- **[FFmpeg Integration](ffmpeg.md)** - How transcoding works and when it is needed.
- **[Rate Limiting & Edge Redirection](rate_limiting_and_edge_redirection.md)** - Protect and scale stream delivery under heavy traffic.
- **[Dependencies & Licenses](dependencies.md)** - Detailed list of used open-source crates and their licensing.
