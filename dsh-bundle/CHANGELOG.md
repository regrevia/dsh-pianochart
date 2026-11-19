# Changelog

## 0.1.0

First release.

- Host tool `piano_score`: one audio source in, score out.
- Skill `pianochart`: which tier to pick, previewing long audio with `seconds`,
  handing a page link to Envoy when the tool reports it is not an audio file.
- MCP server `pianochart` on stdio, so other MCP clients can call the same
  pipeline.
- Outputs per tier: MusicXML, MIDI, numbered notation text, page PNGs and a
  piano-roll image.
