# Steam DASH Clip Extractor (`steam-dash-clip-extractor`)

Extracts clean `.mp4` videos from Steam's native Game Recording `clip_*` folders (`session.mpd`).

## Why?

Steam Game Recording stores clips as fragmented DASH chunks with a non-zero `start="PT..."` attribute inside `session.mpd`, which causes standard FFmpeg invocations to output corrupt or empty files.

`steam-extract-clips`:
- Rewrites the MPD `start` offset to `PT0.0S` cleanly in a temporary manifest before muxing/transcoding.
- Runs as a background `--watch` service that waits until active games exit (zero CPU/NVENC contention mid-match), or triggers on demand via hotkey (`trigger-clip-extraction --force`).
- Maintains `.extracted_history.json` so clips you delete from the export folder are never re-extracted automatically.
- Supports direct lossless `-c copy` or automatic NVENC downscaling (`--height 1440`, `--height 0` for native).
- Optional `--mute-ui-chimes` stubbing for Steam toast `.wav` sounds.

## Install

```bash
install -Dm755 steam-extract-clips trigger-clip-extraction ~/.local/bin/
install -Dm644 systemd/steam-extract-clips.service ~/.config/systemd/user/
systemctl --user daemon-reload && systemctl --user enable --now steam-extract-clips.service
```

Customize input/output folders via flags (`--clips-dir`, `--output-dir`, `--appid`) or environment variables (`STEAM_CLIPS_DIR`, `STEAM_EXPORT_DIR`).

## License

GPL-3.0-or-later — [labs.yuup.com](https://yuup.com/labs)
