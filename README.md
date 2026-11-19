# pianochart

One audio link in, one playable piano score out: five-line staff images,
numbered (jianpu) notation, MusicXML and MIDI, in three tiers that share one
arrangement.

```
audio -> source separation (Demucs) -> melody and bass (pYIN, chroma)
      -> arrangement (three tiers) -> staff + numbered notation
```

![First page of a score](docs/img/easy-p01.png)

## What it does and does not do

- Transcribes the background music only. Vocals are separated out and dropped.
- A drum kit does not become playable piano. Percussion can only show up as
  rhythmic weight, not as notes.
- Non-melodic instruments that carry harmony are folded into the left hand.
- Three tiers share one arrangement: same rhythm, same density, same register.
  Only the key signature differs, so `easy` has the fewest black keys and is the
  tier to play. Playback restores the original pitch.

Both hands are written to be playable: at most three keys under one hand at a
time, never wider than an octave.

## Install

The pipeline is Python; the DSH plugin is a thin bundle around it. You need both.

```bash
git clone https://github.com/regrevia/dsh-pianochart.git
cd dsh-pianochart

# 1. Python environment
python3 -m venv .venv
.venv/bin/pip install -r requirements.txt
brew install cairo            # macOS, for cairosvg

# 2. Separation weights, into models/demucs_repo/ (84 MB, not in the repo)
#    Demucs normally downloads these; offline you fetch them once:
mkdir -p models/demucs_repo
curl -L -o models/demucs_repo/955717e8-8726e21a.th \
  https://huggingface.co/akhaliq/Demucs/resolve/main/955717e8-8726e21a.th
printf "models: ['955717e8']\n" > models/demucs_repo/htdemucs.yaml
```

`models/demucs_repo/` holds exactly two files: the `.th` state dict and the
`htdemucs.yaml` index. Point the pipeline elsewhere with `--demucs-repo`.

## Run the pipeline directly

```bash
PYTHONPATH=src .venv/bin/python -m pianochart.cli \
  --input track.m4a --preset all --out out/track
```

Options: `--seconds N` analyse only the first N seconds; `--no-separate` skip
separation for a fast try; `--preset easy|medium|hard|all`; `--transpose N`;
`--out DIR`; `--cache-dir DIR`; `--demucs-repo DIR`; `--device cpu|mps`.

Output per tier in `out/<name>/`: `.musicxml`, `.mid`, `.jianpu.txt`, `-p01.png`
staff pages, `-roll.png` piano roll, and `report.json`.

## Use it from DeepSeek Harness

Install the bundle, then call the host tool `piano_score`:

```json
{ "source": "https://example.com/track.mp3", "tier": "easy" }
```

- `source` is an audio file URL, a local path, or a page URL (the plugin tries to
  find the audio on it and hands over to Envoy when the page needs a browser).
- `seconds` for a preview of long audio; `jianpuChars` caps the numbered
  notation returned in chat; `transpose` shifts the written key.
- Outputs land in a new directory under `out/`; the absolute paths come back.

The same pipeline is also served over MCP as `piano_score` on stdio, so any MCP
client can use it. The bundle starts it from `lib/pianochart_mcp.py`; point it at
your checkout with `PIANOCHART_REPO` if it is not next to the plugin.

## Reading the numbers

`report.json` carries the arrangement statistics and two similarity figures
against the source: `chroma_cos_mean` (tonal agreement over the track) and
`chroma_cos_p10` (worst tenth). They measure pitch content, not playability. A
short clip scores lower for lack of material.

## What is not done

MuseScore or LilyPond PDF rendering, AMT refinement (Transkun, basic-pitch),
drum and beat grid refinement, multi-voice left-hand textures.

## License

MIT. The Demucs weights are MIT and remain under their own license; they are
downloaded, not redistributed here.
