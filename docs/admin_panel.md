# Web Admin Panel (/admin)

The Web Admin Panel provides an integrated real-time dashboard directly on the delivery port (e.g. `http://localhost:8080/admin`). It allows operators to monitor active streams, manage connected listeners, and view historical statistics.

## Configuration

The Admin Panel is configured in the global configuration section of `config.xml` via the `<admin-panel>` element:

```xml
<global>
    <!-- 
      admin-panel:
      @username: Login username for the Admin Panel. (No default - empty/disabled if not specified)
      @password: Login password for the Admin Panel. (No default - empty/disabled if not specified)
      @retention-days: Number of days for which historical statistics are kept in the database.
                       Default: 730 (2 years). The oldest entries are automatically overwritten.
      @allow-config-editing: Allows direct editing and reloading of the configuration file via the Web UI.
                             Default: false (disabled by default for security reasons).
    -->
    <admin-panel username="myadmin" password="supersecretpassword" retention-days="730" allow-config-editing="false" />
</global>
```

> [!IMPORTANT]
> Without explicitly specifying `username` and `password` in the configuration, the Admin Panel is **completely disabled** for security reasons (returning a 404).

---

## Security and Licensing Prerequisites

1. **License Validation**: The Admin Panel is a premium feature. Access requires a valid SPCast license. Without a license, `/admin` returns an HTTP `403 Forbidden` status.
2. **Authentication**: Access is protected via HTTP Basic Auth.
3. **Reserved Path**: The path `/admin` (and `/admin/`) as well as all channel IDs and mount points named `admin` or `/admin` are blocked system-wide. Attempts to define a mount point named `/admin` will result in a configuration load error and prevent the server from starting.

---

## Features

### 1. Dashboard Overview (Real-Time)
- Display of server uptime, version, and license status.
- Total number of all connected listeners in real-time (HLS + Direct streams).
- List of all registered channels with their live or offline status and current listener count.

### 2. Detailed Listener List (ListClients with Tabs)
For each active channel, there is a detail view with two dedicated tabs:
- **Direct Listeners**: Shows all listeners receiving the stream directly via the Shoutcast/Icecast protocol (MP3/AAC/Opus passthrough). Information includes IP address, User-Agent, selected stream variant, connection duration, and transferred data volume.
- **HLS/DASH Viewers**: Shows the estimated HLS and DASH viewers (separated into IP address and User-Agent columns) since these protocols are stateless.

### 3. Kick & Move (Direct Streams)
- **Kick Client**: Disconnects a specific listener session immediately via an internal oneshot shutdown signal.
- **Move Client**: Allows moving a listener from one channel to another. Since the player reconnects to the same host, the server stores a temporary redirect mapping (30s validity) for the client's IP address and closes the current connection. Upon reconnection, the client is automatically forwarded to the target channel.

> [!NOTE]
> HLS and DASH viewers cannot be individually kicked or moved due to the nature of stateless segments. The Admin UI hides these options for HLS listeners.

### 4. Configuration Editor (Secure Reload)
- In the "Config Editor" tab, the `config.xml` can be edited directly in the web interface and loaded via "Save Config" or "Reload & Apply Config".
- **Security Enablement**: This feature is disabled by default for security reasons. It must be explicitly unlocked via the `allow-config-editing="true"` attribute in the `<admin-panel>` tag. Otherwise, the tab is hidden in the sidebar menu, and all read/write access to the raw configuration is rejected with a `403 Forbidden`.
- **Security Validation**: To prevent system crashes or lockouts due to a malformed XML file, the server first writes the input to a temporary file (`config.xml.tmp`) and validates it. Only upon successful parsing is the main configuration overwritten and can be hot-reloaded.

### 5. Historical Statistics and Events (Stats DB)
- The server writes minutely snapshots of listener counts to a local SQLite database (`data/stats.db`).
- The data is automatically aggregated:
  - Data older than 7 days: 10-minute aggregation.
  - Data older than 90 days: hourly aggregation.
  - Data older than `retention-days` (default: 2 years): Automatic deletion of entries (FIFO).
- The Admin Panel visualises the historical server utilization using an interactive SVG graph.
- **Event Log**: Important system events (such as broadcaster connection establishment `"live"`, termination `"offline"`, metadata changes `"metadata"`, configuration reloads `"config_reload"`, and server starts) are permanently recorded in the SQLite database and can be viewed via the "Recent Events" tab in the statistics section.

---

## API Reference (Internal)

All API routes are secured via Basic Auth and return JSON:

- `GET /admin/api/overview` - Global server uptime and listener counts.
- `GET /admin/api/channels` - Status list of all channels.
- `GET /admin/api/channels/{id}/listeners/direct` - JSON array of all direct listeners on channel `{id}`.
- `GET /admin/api/channels/{id}/listeners/hls` - List of estimated HLS users on channel `{id}`.
- `POST /admin/api/channels/{id}/kick` - Kicks direct listener. Body: `{"session_id": "string"}`.
- `POST /admin/api/channels/{id}/move` - Redirects direct listener. Body: `{"session_id": "string", "target_channel": "string"}`.
- `GET /admin/api/stats/peak?channel_id={id}` - Peak listener count for a channel (or global if `channel_id` is missing).
- `GET /admin/api/stats/history?channel_id={id}&granularity={minute|10min|hourly}&from={unix_ts}&to={unix_ts}` - Historical data points for charts.
- `GET /admin/api/stats/events?channel_id={id}&limit={50}` - Returns the last X system events from the log.
- `GET /admin/api/config` - Returns the current server configuration (passwords and secrets redacted).
- `GET /admin/api/config/raw` - Returns the raw XML content of `config.xml` (requires `allow-config-editing="true"`).
- `POST /admin/api/config/raw` - Saves and validates raw XML content (requires `allow-config-editing="true"`). Body: `{"raw_xml": "string"}`.
- `POST /admin/api/reload` - Triggers a manual hot reload of `config.xml` (requires `allow-config-editing="true"`).

---

## 🔒 Security and Operational Risks

### 1. SSRF Risk with Pull Ingest
When starting an HTTP Pull Ingest (`POST /admin/channels/{id}/ingest/pull/start`), the server intentionally allows specifying local or private IP addresses (e.g. `127.0.0.1` or `localhost`), as this is necessary for relaying local audio sources on the same server.
* **Operational Risk**: If the administrator credentials are compromised, there is a risk of Server-Side Request Forgery (SSRF).
* **Mitigation**: Secure the Admin API and the Admin Panel with strong passwords and restrict access to trusted IP networks.

### 2. Reverse Proxies and IP Header Trust
By default, the server ignores headers like `X-Forwarded-For` and `X-Real-IP` to prevent IP spoofing.
* **Configuration**: Use the `<trusted-proxies addresses="127.0.0.1,..." />` element in the global configuration to mark the IP addresses of your reverse proxies (e.g., Nginx or Cloudflare) as trusted if you require IP-based rate limiting or accurate listener location statistics.
