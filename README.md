# G&W Music Player — v1.0


Standalone Retro-Go SD homebrew music player for the Game & Watch.
Author: **dadagm**

![player-menu-green-320x240.png…]()


- Version: `v1.0` (single source of truth: `CORE_VERSION` in the Makefile)
- 320×240 interface, light-green and light-red skins, large 8×8 bitmap text
- MP3 (minimp3) and PCM WAV
- `/music` folder on the SD card
- remembers the last played file in `/music/.last`
- B saves the play position (settings key `pos_ms`) and the next launch resumes
  from it, unless it was under 3 s or within 3 s of the end
- an empty `/music` shows a "no music found" modal; B returns to the launcher
- left/right tap = previous/next track, hold = seek ±5 s
- A = play/pause, TIME = cycle one/shuffle/all, GAME = menu, B = exit
- Up / Down adjusts the system volume (VOL key on the key bar)
- progress bar with elapsed / remaining time, track title/artist, live spectrum bars
- long titles scroll (marquee) inside the 188 px title column instead of being cut
- battery gauge and wall clock in the header
- Simplified Chinese UI and GB2312 level-1 song titles
- GAME opens a modal menu (language / skin / close); B or GAME closes it


## SD layout

```text
SD:/music/song.mp3
SD:/music/song.wav
SD:/music/.last              (written automatically)
SD:/homebrew/MusicPlayer.bin
```

The packed `.bin` already contains the cover; the external `.img` is only
needed if you want to replace it.

## Controls

| Button | Action |
|---|---|
| Left / Right | Tap (<550 ms, on release): previous/next track; hold (>=550 ms): seek -/+5 s, repeating every 450 ms |
| A | Play / pause |
| Up / Down | Volume + / - |
| TIME | Cycle R1 → SHUF → ALL |
| B | Exit to launcher (`odroid_system_switch_app(0)`) |
| PAUSE/SET | Retro-Go system menu |
| GAME | Open/close the app menu (language / close) |

## Build

Requirements:

```text
arm-none-eabi-gcc (v10+, hard-float fpv5-d16)
GNU Make
Python 3 + Pillow
```

Local build:

```bash
make PROJECT_KIND=homebrew
```

Windows (Python is usually `python`, not `python3`):

```bat
make PROJECT_KIND=homebrew PYTHON=python
```

Docker build:

```bash
make docker PROJECT_KIND=homebrew
```

Output:

```text
MusicPlayer.bin
```

Copy it to `/roms/homebrew/MusicPlayer.bin`.

## Notes

- The first version decodes MP3 and PCM WAV. FLAC and OGG are intentionally
  left for the next iteration; the UI and playback loop do not depend on them.
- Files are scanned at startup. Add or remove music, then restart the app.
- The header shows the RTC clock (HH:MM) and the battery level; both are polled
  once a second. The clock reads `--:--` if the RTC has no time set.
- The progress bar shows elapsed time on the left and remaining time (`-M:SS`)
  on the right.
- UI labels and GB2312 level-1 Chinese titles are rendered from a 14×14
  bitmap subset. Rare characters outside GB2312 level 1 show as `?`.
- Default language is English. Open GAME → Language → A to switch to
  Simplified Chinese; the choice is stored in app settings.
- MP3 duration is estimated from the first frame's bitrate; it is close, not
  sample-accurate.
- The cover is `src/assets/cover.jpg` (176×96 — both sides a multiple of 16,
  4:2:0 JPEG ≤10 KiB). A size that is not 16-aligned gets padded to the next
  MCU row by the firmware HW decoder, and those padded rows show up as a few
  rows of mosaic blocks under the tile. Regenerate with
  `python tools/gen_cover.py --preview out.png`.

