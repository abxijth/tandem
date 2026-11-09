# Using Tandem

## Connecting

Run `tandem`, choose **Start Tandem**, then pick how the phone reaches the computer.

- **USB cable** (lowest latency). Turn on *USB debugging* on the phone and plug
  it in. Tandem sets up `adb reverse` by itself. This works even on mobile data.
- **USB tethering.** *Settings → Hotspot & tethering → USB tethering*. No
  developer options needed.
- **Wi-Fi.** The phone and computer must be on the same network. The port is
  5000, or the next free one. If a firewall blocks it, the live view says so,
  and you can press **f** to open the port.

While it waits, the live view shows connection checks: whether USB debugging is
authorised, whether a firewall is in the way, and whether Wi-Fi is reachable.

**Best Wi-Fi tip:** connect the computer to the phone's own mobile hotspot. The
two talk directly, with no router, no other devices and no guest network
isolation. The computer gets internet through the phone's data, but the video
itself stays on the hotspot link and uses no mobile data.

In the app, tap **Scan QR code**, scan it, and tap **Connect**. The phone's stock
camera also works: it opens a `tandem://` link in the app, which asks you to
confirm the computer. To type an address by hand, also type the pairing code
shown under the QR code.

## Touch

- **GNOME:** nothing to set up.
- **Hyprland:** touch needs a one-time permission for `/dev/uinput`. The
  installer offers to set it up, or press **t** in the live view.

## The terminal UI

- **Start Tandem** launches the daemon and opens the live view. `q` goes back to
  the menu and keeps streaming. `d` disconnects and removes the monitor.
- **Configure** sets backend, side (left, right, up, down), resolution (or
  *Match connected phone*), FPS, scale, quality, keyframe interval, preferred
  link, port, touch on or off, and paired phones. *Forget all* makes a new
  pairing key, so every phone has to scan again. Side and scale apply live
  while streaming. Other changes restart the stream. Settings live in
  `~/.config/tandem/config.json`.
- **Dependencies** lists what is missing for your distro and backend. Press `i`
  to install it.
- **Detected hardware** shows the desktop, monitors, VA-API node, USB tether
  link, phone panel size and LAN IP.

Without the TUI, `~/.local/share/tandem/linux/start-tandem.sh usb` prints the
QR code and runs the daemon in the foreground.

## Daemon flags

```bash
cd linux
./start-tandem.sh usb|wifi [flags]
python3 tandemd.py --help
```

```
--backend auto|hyprland|gnome            auto-detected from the session
--width 2400 --height 1080 --fps 60      virtual monitor mode
--side right|left|up|down --scale 1.5    placement (applied live on SIGUSR1)
--bitrate 12M --gop 30                   encoder (Hyprland: GOP is at least 1 s of frames)
--encoder auto|vah264enc|x264enc|...     GNOME: GStreamer element
--encoder auto|h264_nvenc|h264_vaapi|libx264|libopenh264   Hyprland
--device /dev/dri/renderD128             Hyprland: VA-API node
--host 0.0.0.0 --port 5000               default: every interface, IPv4 and IPv6
--keep-output                            Hyprland: keep the monitor when the daemon exits
--parent-pid PID                         stop and remove the monitor when PID exits
--pairing-key-file ~/.config/tandem/pairing-key
--tls-dir ~/.config/tandem/tls           created if missing
--no-touch                               view only
```

Signals: SIGTERM, SIGINT and SIGHUP stop the daemon cleanly. SIGUSR1 re-reads
`--banner-file` and applies side and scale live.

## Who can connect

The QR code carries a random pairing key and the fingerprint of the computer's
TLS certificate. Nothing is sent to a phone until both sides prove they know the
key. Video and touches are encrypted. Other devices on the network can neither
watch the screen nor send touches. Details are in [SECURITY.md](SECURITY.md).

## Coming from Screen Extender (amVD 0.4 or older)?

Tandem is a separate app with its own package ID, so it installs next to the
old one. Uninstall *Screen Extender*, then scan the new QR code. The
`-debug.apk` is another separate app, called *Tandem (debug)*, for testing.
