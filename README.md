# Financial Short Video Generator

Auto-generates vertical short-form videos (720×1280) covering daily financial
news as a dialogue between two anime characters — **gugugaga** (the experienced
analyst) and **meowchan** (the curious beginner). News fetch, script writing,
voice synthesis, chart rendering, and video composition all happen in one
pipeline run.

## Pipeline

```
news fetch  →  dialogue (LLM)  →  TTS  →  chart  →  video composition
```

| Stage | Module | Engine |
|------|--------|--------|
| News + Chinese dialogue | `news_fetcher.py` | Anthropic Claude API w/ web_search |
| Voice synthesis (anime) | `tts_generator.py` | VITS Umamusume (HuggingFace Space, free) |
| Price chart | `chart_generator.py` | Plotly + Kaleido |
| Video composition | `video_composer.py` | MoviePy 2.1 + Pillow |
| Orchestration / CLI | `main.py` | — |

Output is a vertical 720×1280 MP4 sized for TikTok / YouTube Shorts /
Instagram Reels, with a dimmed background loop, two character sprites that
swap who's speaking, a topic banner, optional chart overlay, and Chinese
subtitles synced to the TTS audio.

## Requirements

- Python 3.10+
- An Anthropic API key (used for news + dialogue generation)
- Internet access for the HuggingFace VITS Space
- Windows CJK font (auto-detected) or set `FONT_PATH` env var on other OSes
- Background video at `backgrounds/<name>.mp4` (path configured in `video_composer.py`)
- Character sprites at `characters/character1.png` and `characters/character2.png`

Asset folders (`backgrounds/`, `characters/`, `experiment-assets/`) are
gitignored — supply your own.

## Setup

```bash
pip install -r requirements.txt
cp api-key.env.example api-key.env
# edit api-key.env and paste your Anthropic key
```

## Usage

```bash
python main.py
```

Menu:

| Key | Mode |
|-----|------|
| `1`–`4` | Generate from a preset topic (crypto, US tech, AI, global markets) |
| `5` | Custom topic |
| `t` | Test mode — reuse cached dialogue + audio (no API cost) |
| `r` | Rebuild mode — regenerate audio from cached dialogue |
| `v` | Configure VITS speakers for each character |
| `q` | Quit |

Output lands in a timestamped `output_YYYYMMDD_HHMMSS/` folder containing
`output.mp4`, `dialogue.json`, `chart.png`, and per-line audio files.

### Voice configuration

Press `v` on first run to pick a VITS speaker for each character. The
HuggingFace Space exposes hundreds of anime/game voices; selections are saved
to `voice_config.json` and applied automatically on subsequent runs.

## Project layout

```
.
├── main.py                  # CLI orchestration
├── news_fetcher.py          # Claude web_search → JSON dialogue
├── tts_generator.py         # VITS Umamusume HTTP client
├── chart_generator.py       # Plotly chart rendering
├── video_composer.py        # MoviePy composition
├── voice_config.json        # Persisted speaker assignments
├── requirements.txt
├── api-key.env.example
├── backgrounds/             # (gitignored) background loops
├── characters/              # (gitignored) character sprites
├── experiment-assets/       # (gitignored) cached test inputs
└── output_*/                # (gitignored) generated runs
```
