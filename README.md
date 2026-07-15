# SPCast Media Edge

Welcome to the official release of `SPCast.Server` (SPCast Media Edge).

## System Overview

SPCast Media Edge is a high-performance, modern streaming edge server built entirely from the ground up in **100% pure Rust**. 
It is engineered for extreme efficiency designed to run close to the metal with near-zero CPU footprint on idle and minimal resource utilization even under heavy load.

**Zero System Dependencies & True OS Independence**
The core routing and connection handling of SPCast Media Edge operates completely stand-alone. There are no external dependencies, no complex runtimes, and no background services required. Because it is a compiled, self-contained binary, **it does not matter if you use Debian, Ubuntu, Windows, macOS, or any other modern operating system**. It will simply run everywhere without requiring specific system packages or OS-level dependencies. You just download the binary, run it, and start streaming!

We've been using the server internally for a few years now. Since January 2024, we've also been running it in SPCAST hosting environments at [www.spcast.eu](https://www.spcast.eu). SPCAST Media Edge is tailored to our own needs, but it's also a great fit for external platforms or private hobby stations. The configuration is quite extensive but understandable and does require some technical understanding. If you run into any hurdles getting started that we as developers might not notice, feel free to let us know.

## Licensing & Limits
- **Free Tier:** Using the media edge server is completely free of charge for up to 100 concurrent audio slots, or up to 5 concurrent video slots.
- **Commercial:** If you require more concurrent slots, a valid license must be provided in your configuration. You can acquire a commercial license at: [https://www.spcast.eu/en/server/](https://www.spcast.eu/en/server/)

## Configuration & Usage

Use the `bin` directory for the executable suited to your operating system. Ensure you configure your server using the `config.xml` file placed alongside the executable. 

For detailed usage, configuration options, and API documentation, please refer to the `docs/` and `examples/` directories included in this release.

## Transcoding & FFmpeg (Optional)

SPCast Media Edge natively handles vast amounts of standard streaming protocol routing internally without any external tools.

However, for specific advanced application scenarios specifically **transcoding** audio/video streams into different formats or bitrates on the fly, SPCast Media Edge utilizes **FFmpeg**. 

* **When do you need FFmpeg?** Only if you actively configure stream transcoding features that explicitly require format conversion. For standard operation, FFmpeg is completely ignored.
* **How is it integrated?** If provided alongside the server executable, SPCast Media Edge will independently call FFmpeg via the command line when a transcoding pipeline is required.

> **Please Note regarding FFmpeg**
> The FFmpeg binary is **NOT** provided or bundled with this release due to variable hardware architecture requirements and varying licensing landscapes. If your use case requires transcoding features, you must independently download and provide the correct FFmpeg executable for your system. For detailed instructions on where to get it and how to set it up, please read the included `docs/ffmpeg.md`.

## How to run SPCast Media Edge

Start here: [readme.md](docs/readme.md).
