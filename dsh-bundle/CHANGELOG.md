# Changelog

## 0.1.2

- Peer range for `@deepseek-ai/dsh-mcp-client` covers both prerelease lines
  (`>=0.1.0-rc.1 <0.2.0-0 || >=0.2.0-rc.1 <0.3.0-0`), so installing on a 0.1
  harness build no longer resolves to nothing.

## 0.1.1

- `@deepseek-ai/dsh-mcp-client` moved from `dependencies` to `peerDependencies`:
  the host owns the client, the bundle must not pin a second copy.
- Icon redrawn: one note on a faint staff, replacing the previous mark that
  read as a grid at small sizes.

## 0.1.0

First release.

- Host tool `piano_score`: one audio source in, score out.
- Skill `pianochart`: which tier to pick, previewing long audio with `seconds`,
  handing a page link to Envoy when the tool reports it is not an audio file.
- MCP server `pianochart` on stdio, so other MCP clients can call the same
  pipeline.
- Outputs per tier: MusicXML, MIDI, numbered notation text, page PNGs and a
  piano-roll image.
