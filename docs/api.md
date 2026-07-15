# API Endpoints and Administration

This document lists all available API endpoints, metadata interfaces, and administration routes.
For ingest and playback URLs see [playable_links.md](playable_links.md).

> In all examples `localhost` is used as the hostname. Replace it with your server's IP address or domain name.

---

## 1. Public Delivery API

These endpoints are served by the Delivery Engine (default port `8080`) and are accessible to viewers and players.

### Metadata

| Method | Path | Description |
|---|---|---|
| `GET` | `/api/{channel_id}/metadata/live` | Server-Sent Events stream that pushes real-time title updates for a channel. |
| `GET` | `/api/{channel_id}/history` | Returns the last played tracks for a channel as a JSON array. |
| `GET` | `/api/stats` | Returns active viewer counts per channel and rendition as JSON. |
| `GET` | `/api/streams` | Lists all channels with their current live state as JSON. |
| `GET` | `/api/config/footer` | Returns footer configuration data as JSON (used by the embedded web UI). |

### Web Interfaces

| Method | Path | Description |
|---|---|---|
| `GET` | `/main.html` | The main server dashboard/overview page. |
| `GET` | `/{mount}/player.html` | The integrated HTML5 audio web player (see `player.md`). |
| `GET` | `/video/{channel_id}` | The integrated HTML5 video web player for a specific channel. |
| `GET` | `/{mount}/audioui` | The Icecast emulation player GUI page, serving the Hls.js HTML player directly. |

### Privacy & Transparency

| Method | Path | Description |
|---|---|---|
| `GET` | `/privacy` | Public endpoint displaying the real-time data processing configuration of this server node (logging level, active trackers, active streams). Intended as an independently verifiable privacy notice for listeners. |

### Static File Server

Falls back to files placed in the configured `./web` directory (if `<static enabled="true">` is set in `config.xml`). This is how the embedded web dashboard and custom 404 pages are served.

---

## 2. Prometheus Metrics

| Method | Path | Description |
|---|---|---|
| `GET` | `/metrics` | Exposes Prometheus-compatible system metrics (CPU, RAM, connections, stream state). Default port: `9100`. |

> The metrics port is configured separately via `<metrics>` in `config.xml`.

---

## 3. Internal Admin REST API

> **This API must never be exposed to the public internet.** Bind it to `127.0.0.1` only.
> All endpoints require HTTP Basic Authentication (configured via `<admin username="..." password="...">` in `config.xml`).

Base URL: `http://127.0.0.1:9090`

### System

| Method | Path | Description |
|---|---|---|
| `GET` | `/admin/health` | Returns server health as JSON: `status`, `uptime_secs`, `active_channels`, `total_viewers`, `license_valid`. |
| `GET` | `/admin/version` | Returns the server version string. |
| `GET` | `/admin/config` | Returns the currently active configuration as JSON. Sensitive fields (passwords, secrets) are automatically redacted. |
| `POST` | `/admin/reload` | Reloads `config.xml` from disk and applies changes that are safe at runtime. Returns a diff summary with `applied`, `pending_requires_restart`, and `notes` fields. |

### Channels

| Method | Path | Description |
|---|---|---|
| `GET` | `/admin/channels` | Lists all registered channels and their current state. |
| `GET` | `/admin/channels/{id}` | Returns the state of a specific channel. |

### Ingest Control

| Method | Path | Body (JSON) | Description |
|---|---|---|---|
| `POST` | `/admin/channels/{id}/ingest/pull/start` | `{"url": "http://..."}` | Starts an HTTP Pull ingest for the given channel. The URL is validated against SSRF rules before connecting. Note: Loopback/Private IPs are allowed for relaying local streams; secure admin credentials to mitigate SSRF risk. |
| `POST` | `/admin/channels/{id}/ingest/srt/start` | `{"bind_addr": "0.0.0.0:9001"}` | Starts an SRT listener ingest for the given channel. |
| `POST` | `/admin/channels/{id}/ingest/stop` | - | Stops any running ingest pipeline for the given channel. |
| `GET` | `/admin/channels/{id}/ingest/status` | - | Returns ingest statistics for a channel (bytes received, reconnect count, last error). |

### Recording

| Method | Path | Body (JSON) | Description |
|---|---|---|---|
| `POST` | `/admin/channels/{id}/record/start` | `{"ttl_secs": 3600, "max_bytes": 1073741824}` | Starts a DVR recording session for the given channel with the specified retention policy. |
| `POST` | `/admin/channels/{id}/record/stop` | - | Stops the active recording session for the given channel. |

### Segment Store

| Method | Path | Description |
|---|---|---|
| `GET` | `/admin/store` | Returns aggregate segment store metrics (total segments stored/evicted, current byte usage). |
| `GET` | `/admin/store/{rendition_id}` | Returns the current segment window for a specific rendition (e.g. `live1/audio`). |

### Delivery

| Method | Path | Description |
|---|---|---|
| `GET` | `/admin/delivery/routes` | Lists all active delivery routes and their current viewer counts. |
