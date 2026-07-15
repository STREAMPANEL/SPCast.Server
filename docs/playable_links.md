# Playback & Endpoints

Based on the current project structure (`config.xml` and source code), this document lists all available endpoints, covering both ingest (broadcasting) and playback options.

> In all examples `localhost` is used as the hostname. Replace it with your server's IP address or domain name.

---

## 1. Broadcasting Ingest

How to send a stream to the server (example channel: `test_channel`).

### Icecast Ingest

| Setting | Value |
|---|---|
| Server | `localhost` |
| Port | `8000` |
| Mountpoint | `/live` |
| Username | `admin` (or as defined in the configuration) |
| Password | As configured in `config.xml` (`<icecast password="...">`) |
| Connection URL | `http://localhost:8000/live` |

### Shoutcast v1 / v2 Ingest

| Setting | Value |
|---|---|
| Server | `localhost` |
| Port | `8001` |
| Mountpoint | `/live` |
| Password | As configured in `config.xml` (`<shoutcast password="...">`) |

### SRT Ingest

| Setting | Value |
|---|---|
| Protocol | Secure Reliable Transport |
| Server URL | `srt://localhost:9001` |
| Passphrase | As configured in `config.xml` (`<srt passphrase="...">`) |
| Stream ID | `test_channel` (must match the configured channel ID) |

> SRT is the recommended ingest protocol for low-latency and broadcast-grade reliability. It is supported natively by OBS Studio, FFmpeg, vMix and so on.

---

## 2. Active Channel Playback

### Channel: `test_channel`

#### Embedded HTML5 Web Player

Automatically provided based on the Icecast ingest configuration. Includes live metadata via SSE.

```
http://localhost:8080/live
```

#### Apple HLS (HTTP Live Streaming)

| Variant | URL |
|---|---|
| Master playlist | `http://localhost:8080{mountpoint}/hls/master.m3u8` |
| Media playlist | `http://localhost:8080{mountpoint}/hls/audio/index.m3u8` |
| Individual segment | `http://localhost:8080{mountpoint}/hls/audio/seg_X.m4s` |

#### MPEG DASH

| Variant | URL |
|---|---|
| Manifest | `http://localhost:8080{mountpoint}/dash/manifest.mpd` |
| Individual segment | `http://localhost:8080{mountpoint}/dash/segment_X.m4s` |

#### Continuous Audio Streams (Direct Streams)

Generated exclusively based on the ingest codec or FFmpeg output settings. Serve as zero-CPU passthrough or live transcoding output.

Primary routes (via mount path):

| Format | URL |
|---|---|
| MP3 | `http://localhost:8080{mountpoint}/live.mp3` (or `http://localhost:8080{mountpoint}.mp3`) |
| AAC | `http://localhost:8080{mountpoint}/live.aac` (or `http://localhost:8080{mountpoint}.aac`) |
| OPUS | `http://localhost:8080{mountpoint}/live.opus` (or `http://localhost:8080{mountpoint}.opus`) |

#### Video HLS

For channels with a video output configured, the following HLS endpoints are available:

| Variant | URL |
|---|---|
| Master playlist | `http://localhost:8080{mountpoint}/video-hls/master.m3u8` |
| Individual segment | `http://localhost:8080{mountpoint}/video-hls/seg_X.ts` |

---

## Further Information

For API endpoints, metadata interfaces, and administration routes see **[api.md](api.md)**.
