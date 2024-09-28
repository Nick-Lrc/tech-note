# Download Livestream

## Goal
Download video from livestream websites.

## Limitation
- The author only attempted the solution once (with `Windows 11` & `Chrome`). It may not apply to all cases.
- Tools mentioned may no longer work in the future.
- The solution is CLI-based.

## Prerequisite
- [`Python`](https://www.python.org/) 3.8+: Environment for `yt-dlp`.
- [`yt-dlp`](https://github.com/yt-dlp/yt-dlp): Downloader. Successor of [youtube-dl](https://github.com/ytdl-org/youtube-dl).
- [`FFmpeg`](https://www.ffmpeg.org/): Media converter. Used by `yt-dlp`. To avoid bugs, use [custom builds](https://github.com/yt-dlp/FFmpeg-Builds#ffmpeg-static-auto-builds).
- Browser: For stream URLs and optional extensions.

Optional
- [`The Stream Detector`](https://chromewebstore.google.com/detail/the-stream-detector/iakkmkmhhckcmoiibcfjnooibphlobak): Extract stream URLs.
- [`Get cookies.txt LOCALLY`](https://chromewebstore.google.com/detail/get-cookiestxt-locally/cclelndahbckbenkjhflpdbgdldlbecc): Get cookies for authentication.

## Step 1: Install `yt-dlp`
```bash
pip install yt-dlp
```

See other methods [here](https://github.com/yt-dlp/yt-dlp?tab=readme-ov-file#installation).

## Step 2: Install `FFmpeg`
1. Download a [custom-built](https://github.com/yt-dlp/FFmpeg-Builds?tab=readme-ov-file#downloads) `ffmpeg` for `yt-dlp`
    - :warning: __Not__ the python package. 
2. Locate `ffmpeg.exe` and add its parent directory to the `PATH` system variable. (e.g. `C:\Program Files\ffmpeg\bin`)
    - :warning: Backup `PATH` first.

## Step 3: Get Stream URLs
### With Extension
- Get the stream URLs ended with [`m3u8`](https://en.wikipedia.org/wiki/M3U) from `The Stream Detector` extension.
  - `m3u8` is a playlist file pointing to the actual media.

### Without Extension
- `Inspect` the livestream page and go to the `Network` tab.
- Refresh the page and search for `m3u8` in `Network` to get stream URLs.

## Step 4: Check Available Formats
```bash
yt-dlp <m3u8 url> --list-formats 
```

If encounter the `403: Forbidden` error, pass cookies via the `--cookies` flags. See [Authenticate With Cookies](#authenticate-with-cookies).

Example output:
```bash
ID                   EXT RESOLUTION FPS │   FILESIZE   TBR PROTO │ VCODEC        VBR ACODEC     MORE INFO
────────────────────────────────────────────────────────────────────────────────────────────────────────────
program_audio-Fine   mp4 audio only     │                  m3u8  │ audio only        unknown    [eng] Fine
program_audio-Normal mp4 audio only     │                  m3u8  │ audio only        unknown    [eng] Normal
1889                 mp4 1280x720    30 │ ~  1.75GiB 1890k m3u8  │ avc1.4d401f 1890k video only
3525                 mp4 1920x1080   30 │ ~  3.26GiB 3525k m3u8  │ avc1.4d4028 3525k video only
```

## Step 5: Download
```bash
yt-dlp <m3u8 url> --downloader ffmpeg --hls-use-mpegts -f <format id>
```
- `yt-dlp` suggests `--downloader ffmpeg --hls-use-mpegts` for livestream.
- Set the video/audio to download with the `-f` flag. Use the `ID` given by the `--list-formats` command.
  - e.g. `-f "3525+program_audio-Fine"` will download the `1080p` video with `Fine` audio
  - See the flag documentation [here](https://github.com/yt-dlp/yt-dlp?tab=readme-ov-file#format-selection).
 
## Authenticate With Cookies
### With Cookie Files
1. Export cookies of the livestream page in the [`Netscape`](https://en.wikipedia.org/wiki/Netscape) format using the `Get cookies.txt LOCALLY` extension.
    - `yt-dlp` does not understand cookies in the `JSON` format.
2. Append `--cookies <cookies>.txt` to `yt-dlp` commands.

### Auto Loading
- Append `--cookies-from-browser <browser>` to `yt-dlp` commands.
    - May fail due to the [`DPAPI ERROR`](https://github.com/yt-dlp/yt-dlp/issues/10927).

## Reference
1. [How to download \[...\] embedded video?](https://www.reddit.com/r/youtubedl/comments/1beiy6w/comment/kutkvhj/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button): Manually extract stream URLs.
2. [Downloading from \[livestream site\]](https://www.reddit.com/r/youtubedl/comments/1bmbvji/downloading_from_spwn/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button): Extension for getting stream URLs.
3. [Has anyone managed to save a paid VOD \[...\]?](https://www.reddit.com/r/youtubedl/comments/thtmv5/comment/i1e92o2/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button): Extension for getting cookies.
4. [Installing FFmpeg on Windows](https://phoenixnap.com/kb/ffmpeg-windows): Add FFmpeg to `PATH` on Windows.
