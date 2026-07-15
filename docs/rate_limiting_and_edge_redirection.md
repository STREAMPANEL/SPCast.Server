# Rate Limiting, Edge Redirection, and Bitrate Enforcement

This document describes the three stream protection and traffic management systems in SPCast Media Edge.

---

## 1. Rate Limiting

The Rate Limiting system prevents flooding and connection spam (e.g. opening hundreds of browser tabs) by limiting the request rate of clients.

### How it Works
- Checks request rates using a composite key: **IP Address + User-Agent String**.
- This NAT-aware design allows multiple distinct devices in the same household/network (sharing the same public IP) to stream simultaneously as long as they have different User-Agent strings.
- **Security & Performance:** The User-Agent string is truncated to a maximum of 128 characters to prevent Denial-of-Service (DoS) memory exhaustion from maliciously crafted, oversized headers.
- **Automatic Garbage Collection:** A periodic background cleanup task runs every 30 seconds to evict expired rate limit state records and prevent memory leaks.

### Configuration
Configure the rate limiter within the `<auth><limits>` block in `config.xml`:

```xml
<auth>
    <limits>
        <!-- Maximum requests allowed in the window -->
        <rate-limit-requests value="40" />
        <!-- Duration of the rate limiting window in seconds -->
        <rate-limit-window-secs value="10" />
    </limits>
</auth>
```
To disable rate limiting, set `<rate-limit-requests value="0" />`.

### Client Response
When a client exceeds the limit, the server responds with:
- **HTTP Status:** `429 Too Many Requests`
- **Content-Type:** `text/plain`
- **Body:** `Too Many Requests (Rate limit exceeded)`

---

## 2. Edge Redirection

Edge Redirection allows the primary origin server to offload excess streaming traffic to configured Edge/CDN nodes once a local viewer threshold is reached.

### How it Works
- When a new HLS (`master.m3u8`), DASH (`manifest.mpd`), or HLS Video request is received, the server checks the active viewer count for that channel.
- If the current local viewer count is greater than or equal to `max-local-viewers`, the server issues a `307 Temporary Redirect` to a randomly selected edge node from the configured list.

### Configuration
Configure the redirection in the `<global>` block of `config.xml`:

```xml
<global>
    <edge-redirection enabled="true" max-local-viewers="50" bypass-header-name="X-SPCast-Origin" bypass-header-value="true">
        <edge-node url="https://edge1.yourdomain.com" />
        <edge-node url="https://edge2.yourdomain.com" />
    </edge-redirection>
</global>
```

### Bypass Mechanisms
Authorized clients (like monitoring services or players requiring origin access) can bypass edge redirection:
1. **Query Parameter:** Append `?origin=true` or `?origin=1` to the request URL.
2. **HTTP Header:** Send the configured bypass header (e.g. `X-SPCast-Origin: true`).

---

## 3. Bitrate Enforcement

The Ingest Bitrate Enforcement system ensures that broadcasters do not exceed their license's maximum ingest bandwidth limit.

### How it Works
- The server continuously monitors the incoming byte stream for every ingest channel (Icecast, Shoutcast, SRT, etc.).
- The max allowed ingest bitrate is cryptographically verified from the active license key.
- **Unlicensed Fallback:** In unlicensed mode, the maximum bitrate is capped at **1900 kbps** (suitable for high-quality audio streams but preventing high-bitrate video abuse).
- **Tolerance Buffer:** Includes a **15% tolerance** buffer to allow for natural network jitter and temporary traffic bursts.
- **Debounce Mechanism:** The broadcaster is allowed **3 consecutive violations** (approx. 15 seconds) before the server terminates the connection to prevent sudden disconnects due to transient network spikes.

### Configuration
This limit is enforced cryptographically via the license and **cannot be configured locally** in `config.xml`.
- **Licensed Instances:** The maximum allowed bitrate is determined by the active license plan.
- **Unlicensed Instances:** Falls back automatically to the hardcoded **1900 kbps** limit.
