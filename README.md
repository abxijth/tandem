<div align="center">

<picture>
  <source media="(prefers-color-scheme: light)" srcset="logo/tandem-logo-black.png">
  <img src="logo/tandem-logo.png" alt="Tandem" width="720">
</picture>

### Your Android phone, as a real second monitor for Linux

Low latency, GPU accelerated, with touch. Drag a window onto the phone and it appears there.

**[Website](https://saharshbaiju.github.io/tandem/)** · **[Download the app](https://github.com/saharshbaiju/tandem/releases/latest)** · **[Docs](#docs)**

![Tandem demo](docs/demo.gif)

</div>

---

Tandem turns an Android phone into a second monitor for a Linux desktop. Think
spacedesk, but native, and the video path stays on the GPU from start to finish.

- **Fast.** About 25 to 45 ms glass to glass over USB, 30 to 60 ms over Wi-Fi.
- **Touch works.** Tap, drag, scroll and pinch. Your mouse pointer stays put.
- **USB or Wi-Fi.** USB cable, USB tethering, or the same network.
- **Safe.** Only a phone that scanned your QR code can connect. Everything is
  encrypted with TLS.
- **Your monitors are left alone.** Tandem only adds a monitor beside them.

## Supported desktops

| Desktop | Distributions |
| --- | --- |
| **GNOME on Wayland** | Ubuntu 22.04+, Fedora 36+, any GNOME 40+ |
| **Hyprland** | Arch, or anything else running Hyprland |

On Ubuntu and Fedora, log in to the Wayland session (the default). On Ubuntu that
means "Ubuntu", not "Ubuntu on Xorg".

## Install

**On the computer:**

```bash
curl -fsSL https://raw.githubusercontent.com/saharshbaiju/tandem/main/install.sh | bash
```

You can read [`install.sh`](install.sh) first if you like. Open a new terminal
afterwards. Run the same line again to update, or `./install.sh --uninstall` to
remove it.

**On the phone (Android 8+):** download `Tandem-<version>.apk` from the
[latest release](https://github.com/saharshbaiju/tandem/releases/latest), open
it, and allow installs when asked.

## Use it

1. Run `tandem` and choose **Start Tandem**. A QR code appears.
2. Plug the phone in over USB (turn on USB debugging for the lowest latency), or
   put both devices on the same Wi-Fi.
3. In the app, tap **Scan QR code**, scan it, and tap **Connect**.

That's it. The phone is now a second monitor. More options, such as Wi-Fi tips,
touch setup on Hyprland and settings, are in [`docs/USAGE.md`](docs/USAGE.md).

## Docs

- [`USAGE.md`](docs/USAGE.md): connecting, settings, daemon flags, troubleshooting
- [`PLATFORMS.md`](docs/PLATFORMS.md): setup per distribution
- [`HOW_IT_WORKS.md`](docs/HOW_IT_WORKS.md): the pipeline in one page
- [`ARCHITECTURE.md`](docs/ARCHITECTURE.md): protocol, reliability rules, latency
- [`SECURITY.md`](docs/SECURITY.md): pairing and encryption
- [`ANDROID.md`](docs/ANDROID.md) and [`DEVELOPING.md`](docs/DEVELOPING.md): building and hacking on it
- [`RELEASING.md`](docs/RELEASING.md): how releases are made

## Roadmap

- RTP over UDP with error correction for bad Wi-Fi
- HEVC in the app
- KDE Plasma backend
- Pen input and typing from the phone

## License

[GPL-3.0](LICENSE) with a few extra terms, written out in [NOTICE](NOTICE):
keep the credit line, don't call a fork "Tandem", and linking with Google Play
services is allowed.

Copyright (C) 2026 Saharsh Baiju.
