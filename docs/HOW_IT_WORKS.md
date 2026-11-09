# How it works

```
virtual monitor (Hyprland headless output / Mutter virtual monitor)
        │  zero-copy capture
   H.264 encode on the compositor's GPU (NVENC / VA-API, x264 / openh264 fallback;
                GStreamer VA / x264 / openh264 on GNOME)
        │  no B-frames, one slice per frame, CBR with a 2-frame buffer
   tandemd.py   splits NAL units, caches the current GOP, one I/O
        │         thread per phone, 1 s heartbeats, keyframe on request (KEYQ),
        │         restarts the encoder if it exits
   TLS over TCP: USB tethering, Wi-Fi, or adb reverse (every QR address tried
        │         in parallel); pairing-key proof before anything is sent
        │                                        ▲
   Android app → MediaCodec → Surface            │ touches, keyframe requests
                hardware decode, rendered on arrival    (paired phones only)
                                                 │
   phone_input.py → virtual touchscreen: uinput (Hyprland) / Mutter RemoteDesktop (GNOME)
```

Target latency from glass to glass is about 25 to 45 ms over USB and 30 to 60 ms
over Wi-Fi, with hardware encoding. Wi-Fi is lowest on the phone's own hotspot.

## Why it is fast

The video goes straight from the encoder to the phone's hardware decoder. There
is no media player, no jitter buffer and no container, just raw H.264. The
encoder runs on the GPU the compositor renders on, so frames are never copied
between GPUs. See [ARCHITECTURE.md §2](ARCHITECTURE.md#2-why-the-pipeline-avoids-buffering).

## Why it stays connected

- Heartbeats run in both directions.
- A phone that joins a still desktop gets the current picture at once, because
  the daemon replays the last keyframe and the frames after it.
- The same replay repairs a broken frame: the phone asks for a keyframe (`KEYQ`)
  and is back on the live picture in milliseconds.
- A phone that falls behind skips stale frames instead of playing them late.
- If the encoder exits, the daemon restarts it and the phone stays connected.
- If the link drops (cable pulled, Wi-Fi roams), the app shows *Reconnecting...*
  over the last frame and resumes by itself.

The full list of rules is in
[ARCHITECTURE.md §7](ARCHITECTURE.md#7-failure-modes-and-the-rules-that-prevent-them).
