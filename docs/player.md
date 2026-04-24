# Integrated HTML5 Web Player

SPCast Media Edge includes a fully integrated, responsive, and modern HTML5 Web Player. It serves as an out-of-the-box listening page for your audience without requiring external CMS systems or third-party web hosting.

## 1. Accessing the Player

By default, the player is served through the Delivery HTTP engine on the start page (e.g., `http://localhost:8080/main.html` or `/player.html`).

If you have multiple channels configured, the player will automatically default to the first *enabled* channel in your configuration. 
To link directly to a specific channel's player, use the `?channel=` URL parameter:

`http://your-server.com:8080/main.html?channel=live1`

## 2. Features

- **Zero-Configuration Playback:** Automatically connects to the best available stream for the requested channel.
- **Real-Time Metadata:** Utilizes Server-Sent Events (SSE) to push current song and artist updates instantly to the browser without polling overhead.
- **Dynamic Cover Art:** Automatically fetches album covers from the Apple Music (iTunes) API based on the currently playing artist and song. Includes smooth UI animations when the track changes.
- **Play History:** Integrates with the `History API` to show listeners the last recently played tracks.
- **Multi-Language Support:** Automatically detects the user's browser language, with manual overrides available via URL parameters.

## 3. Configuration

The player is highly customizable via the `config.xml`. Each channel can have its own customized player appearance. 

Located inside the `<channel>` block, the `<player>` configuration allows you to adjust the visual elements:

```xml
<player enabled="true">
    <radio-name>My Awesome Radio</radio-name>
    <description>The best Radio on the internet streaming globally!</description>
    <logo-url>https://example.com/logo.png</logo-url>
    <background-url>https://example.com/bg.jpg</background-url>
    <favicon-url>https://example.com/favicon.png</favicon-url>
    <no-cover-url>https://example.com/nocover.png</no-cover-url>
    <!-- Custom HTML can be injected into the player body -->
    <custom-html><![CDATA[<a href="https://example.com">Visit our homepage</a>]]></custom-html>
</player>
```

### Global Footer & Privacy
To ensure legal compliance (e.g., GDPR in Europe), the footer of the player is configured globally in the `<global><footer>` block of the `config.xml`. This allows you to define mandatory links for Imprint and Privacy Policy, as well as custom external links.

## 4. Multi-Language System

The player interface is fully localized using `.po` translation files located in the `lang/` directory.

**Detection Priority:**
1. **URL Parameter:** Appending `?lang=de` or `?lang=en` to the URL forces a specific language.
2. **Browser Accept-Language:** If enabled in the global config (`<language auto-detect="true" />`), the server parses the client's browser headers and serves the appropriate translation.
3. **Fallback:** Defaults to the globally configured `default` language (usually `en`) if no matching translation is found.

*Note: Custom metadata (like your `radio-name` or `description` from the config) is not translated automatically.*
