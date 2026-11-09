# Developing

## Test without a real desktop

```bash
python3 linux/testsrc.py --width 2400 --height 1080   # fake desktop, real daemon code
python3 linux/testsrc.py --log-touch                  # also print the touches it receives
adb shell am start -a android.intent.action.VIEW -d "'tandem://c?h=10.0.2.2&p=5000'"   # emulator
adb logcat -s StreamSession
```

`testsrc.py` streams a test pattern through the real broadcaster without
creating a monitor, so you can work on the app on any machine. The emulator
reaches the PC at `10.0.2.2`. More in [ANDROID.md §7](ANDROID.md#7-developer-install-and-debugging).

## Build the Android app

```bash
cd android
JAVA_HOME=/opt/android-studio/jbr ./gradlew :app:assembleDebug
adb install -r app/build/outputs/apk/debug/app-debug.apk
```

Or open `android/` in Android Studio and press Run. The command-line build
needs JDK 17 to 21. If your system JDK is newer, point `JAVA_HOME` at the JBR
that ships with Android Studio. The app needs Android 8.0 or newer. The QR
scanner uses Google Play services, so no camera permission is needed.

## Project layout

```
linux/
  tandem                TUI launcher (installed as the `tandem` command)
  python.sh             picks the system /usr/bin/python3 (not conda/pyenv)
  tui.py                curses UI: deps, detection, config, live view + QR
  core.py               config, distro/dependency detection, addresses, QR URI
  qr.py                 dependency-free QR encoder
  tandemd.py            daemon: backend -> NAL parse -> GOP cache -> TCP broadcast
  secure_link.py        TLS certificate, pairing key, handshake, lock-out
  phone_input.py        touches from phones -> slots
  uinput_touch.py       virtual multi-touch touchscreen via /dev/uinput (Hyprland)
  backends/hyprland.py  headless output, hyprctl, wf-recorder, touch mapping
  backends/xdph.py      NVIDIA: zero-copy capture via the Hyprland portal + NVENC
  backends/gnome.py     Mutter ScreenCast, DisplayConfig, GStreamer, RemoteDesktop
  start-tandem.sh       no-TUI launcher (prints the QR)
  testsrc.py            test-pattern server for app development
  about.py              credit line and license notice (--version)
android/app/src/main/java/io/github/saharshbaiju/tandem/
  MainActivity.kt       Material 3 UI, scanner, transitions, side sheet
  StreamSession.kt      parallel connect, framed reader, MediaCodec -> Surface
  RippleRevealLayout.kt water ripple and reveal (AGSL)
  ConnectTarget.kt      tandem:// QR and deep-link parsing
  SecureChannel.kt      TLS, certificate pinning, pairing proof
  FrameReader.kt        NALU / ASX1 / PING / INPT framing
  FrameWriter.kt        HELO / AUTH / TUCH (phone to computer)
  TouchForwarder.kt     MotionEvent -> touch frames
  AspectRatioSurfaceView.kt  letterboxing
docs/                   PLATFORMS, ARCHITECTURE, ANDROID, SECURITY, RELEASING, ...
install.sh              one-line installer, adds the `tandem` command
```
