# ytgif

Turn a section of a YouTube video into a captioned GIF for sharing in chat apps.

```
ytgif "https://www.youtube.com/watch?v=VIDEO_ID" 0:50 0:53 "FIRST - SECOND - THIRD"
```

The GIF is saved to `~/gifs` (override with `--out-dir` or `$GIF_LIBRARY`).

## How it works

1. `yt-dlp` downloads only the requested time range (at most 720p).
2. `ffmpeg` scales it to 640 px wide, draws the caption in big white capitals with a
   black outline, and encodes a GIF with a per-frame palette for good quality.
3. Captions are one line. Separate phrases with ` - ` or `|` and they appear one after
   another as subtitles, splitting the clip evenly (or `--interval SECONDS` for a fixed time each).

## Install

Needs `ffmpeg` (with `drawtext`), Python 3 and Node.js (used by yt-dlp to solve YouTube's JS challenge).

```
pip install -r requirements.txt
ln -s "$PWD/ytgif" ~/.local/bin/ytgif
```

## Options

| Option | Default | |
|---|---|---|
| `-o NAME` | from caption | output file name |
| `--top` | bottom | put the caption at the top |
| `--width` | 640 | output width in px |
| `--fps` | 25 | frame rate |
| `--mp4` | off | also write a small, higher-quality silent `.mp4` (Telegram plays it as a GIF) |
| `--interval` | clip / phrases | seconds per caption phrase |
| `--max-lines` | 1 | wrap long captions instead of shrinking them |
| `--no-upper` | off | keep the caption's case |
| `--font` | Liberation Sans Bold | path to a `.ttf` (required on macOS/Windows, see below) |
| `--browser` | off | borrow your browser's YouTube login if you hit the bot check (or `$YTGIF_BROWSER`, see below) |

A local video file works in place of the URL.

## Fonts

The default font is Liberation Sans Bold (or Open Sans Bold), looked up at common Linux
paths. On macOS and Windows, or if no font is found, pass one explicitly:

```
ytgif URL 0:50 0:53 "TEXT" --font /path/to/Font-Bold.ttf
```

## "Sign in to confirm you're not a bot"

Depending on your IP address, YouTube may refuse anonymous downloads. If you see this
error, sign in to YouTube in your browser and let `yt-dlp` borrow that session:

```
ytgif URL 0:50 0:53 "TEXT" --browser firefox      # or chrome, brave, edge, ...
export YTGIF_BROWSER=firefox                      # make it the default
```

`--browser` is off by default. What it means for your cookies:

- `yt-dlp` reads your browser's cookie store and uses your YouTube login to make the
  request, so it acts as you on YouTube. Only use it on a machine and browser profile you trust.
- ytgif does not save, copy or upload the cookies. They are read each run and kept in memory.
- YouTube can flag accounts that download automatically (see the
  [yt-dlp FAQ](https://github.com/yt-dlp/yt-dlp/wiki/FAQ)), so consider signing in with a
  secondary account.

Please respect copyright and YouTube's terms of service; this is meant for personal use.
