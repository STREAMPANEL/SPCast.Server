# Installation Guide

This guide covers everything you need to know to get the compiled **SPCast Media Edge** binary configured and running on your system. 

Since the server is fully self-contained and has **zero dependencies**, the installation process is extremely simple: clone the repository, configure, and run.

## 1. Download the Release

The compiled release binaries are hosted on our official public GitHub repository. You can either download the ZIP file from GitHub or clone the repository directly using Git.

Open your terminal or command prompt and run:

```bash
git clone https://github.com/STREAMPANEL/SPCast.Server.git
cd SPCast.Server
```

Inside this directory, you will find the final production structure:
* `bin/` - Contains the compiled executables sorted by OS (`linux`, `windows`, `mac`).
* `config/` - Contains the configuration examples.
* `docs/` - Contains all offline documentation.
* `lang/` - Contains the language files for the web player and admin interface.

## 2. Configuration

The server requires a `config.xml` file in its **working directory** (the folder from which you launch the server) to launch correctly. On first launch, the server automatically provisions all required runtime files:

* **`config.xml`** - If no configuration file is found, the server creates a default `config.xml` based on the built-in minimal example configuration. You can also manually copy one of the example configurations from the `config/` directory.
* **`web/`** - The server extracts embedded web assets (404 page, HLS player JavaScript, override templates) into the `web/` directory. Existing files are never overwritten, so your customisations are preserved.
* **`lang/`** - Translation files are automatically extracted on first launch (see the `lang/override/` directory for customisation options).

1. Copy one of the example configurations from the `config/` directory into the root `SPCast.Server` directory.
   ```bash
   cp config/full_config_example.xml ./config.xml
   ```
2. Open the new `config.xml` with your favorite text editor and customize it to your needs. The default configuration exposes:
   * **Admin API** on `127.0.0.1:9090` (Localhost only, which is safe).
   * **Delivery Engine** on `0.0.0.0:8080` (Where viewers connect).
   * **Metrics** on `0.0.0.0:9100`.

## 3. Commercial License (Optional)

If your deployment demands more than the Free Tier limitation (100 concurrent slots), place your `license.key` file in the root directory (alongside `config.xml`). The server will automatically read and validate the license upon startup.

## 4. FFmpeg for Transcoding (Optional)

The core routing of SPCast Media Edge operates completely stand-alone. However, if you actively configure stream transcoding features (like converting AAC to MP3, or enabling DASH Video), you need to provide **FFmpeg**. You can find more information here [ffmpeg.md](ffmpeg.md).

## 5. Running the Server

Make sure you are in the root directory of the cloned repository.

### Windows
Run the Windows executable via PowerShell or Command Prompt:
```powershell
.\bin\windows\spcast_server.exe
```

### Linux
Make sure the binary is executable and run it:
```bash
chmod +x bin/linux/spcast_server
./bin/linux/spcast_server
```

### macOS
Make sure the binary is executable and run it:
```bash
chmod +x bin/mac/spcast_server
./bin/mac/spcast_server
```

## 6. Setting up as a Systemd Service (Linux)

To ensure the server stays online in the background and restarts automatically if the system reboots, we highly recommend configuring it as a systemd service on Linux.

Create a new service file:

```bash
sudo nano /etc/systemd/system/spcast-server.service
```

Paste the following configuration into the file. Adjust `/path/to/SPCast.Server` to the actual directory where you cloned the repository.

```ini
[Unit]
Description=SPCast Streaming Server Component
After=network.target

[Service]
Type=simple
User=root
# Note: For production environments, running as a dedicated non-root user is strongly recommended.
WorkingDirectory=/path/to/SPCast.Server
ExecStart=/path/to/SPCast.Server/bin/linux/spcast_server
Restart=on-failure
RestartSec=5
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
```

Reload the systemd daemon, enable the service to start on boot, and start it now:

```bash
sudo systemctl daemon-reload
sudo systemctl enable spcast-server.service
sudo systemctl start spcast-server.service
```

You can check the logs to confirm everything booted successfully:

```bash
sudo journalctl -fu spcast-server.service
```

## 7. Verification

Once running, you can verify the systems are online using curl (or by opening the URLs in your browser):

**Get the server health:**
```bash
curl http://127.0.0.1:9090/admin/health
```

**List the configured channels:**
```bash
curl http://127.0.0.1:9090/admin/channels
```

The delivery node will be active on port `8080` (or whatever you configured in the `config.xml`). To test viewing, you can open `http://<your-server-ip>:8080/main.html` in your browser to see the integrated Web-Player.
