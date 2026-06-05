# Dark 2026 for Zed

A faithful port of **VS Code's "Dark 2026"** — the new default dark theme that shipped with VS Code 1.113 — to the Zed editor.

Near-black canvas, light-blue strings, warm-orange parameters, purple functions, green tags, and the signature red structural keywords.

## Install

Search for **Dark 2026** in Zed's extensions (`cmd-shift-x` / `ctrl-shift-x`), install, then pick it in the theme selector (`cmd-k cmd-t`).

## How it was ported

VS Code's `2026-dark.json` inherits through a four-file chain (`dark_modern` → `dark_plus` → `dark_vs`). The colors here were resolved by applying VS Code's TextMate scope-specificity rules across that chain, so inherited colors (teal types `#4ec9b0`, green numbers `#b5cea8`, blue booleans `#569cd6`) render the way VS Code actually displays them — not just the colors listed in the top-level file.

## Recommended: semantic tokens

Part of Dark 2026's look in VS Code comes from LSP semantic tokens (teal module names, orange parameters, blue `const`-bound variables). Zed supports these too — add this to your `settings.json` for the full effect:

```jsonc
"semantic_tokens": "combined",
"global_lsp_settings": {
  "semantic_token_rules": [
    { "token_type": "namespace", "foreground_color": "#4ec9b0" },
    { "token_type": "module", "foreground_color": "#4ec9b0" },
    { "token_type": "decorator", "foreground_color": "#d2a8ff" },
    { "token_type": "parameter", "foreground_color": "#ffa657" },
    { "token_type": "selfParameter", "foreground_color": "#79c0ff" },
    { "token_type": "clsParameter", "foreground_color": "#79c0ff" },
    { "token_type": "variable", "token_modifiers": ["readonly"], "foreground_color": "#79c0ff" },
    { "token_type": "variable", "token_modifiers": ["defaultLibrary"], "foreground_color": "#79c0ff" }
  ]
}
```

Note: Python needs a language server that emits semantic tokens (e.g. basedpyright) for the teal module names.

## Known deviations from VS Code

Zed highlights with tree-sitter rather than TextMate grammars, so a few distinctions can't be reproduced exactly:

- **Keywords**: VS Code splits structural (red) / flow (purple) / operator-like (blue) keywords per TextMate scope. This port maps Zed's capture groups as closely as the grammars allow; languages whose grammar exposes only a plain `@keyword` capture render all keywords red.
- **Variables**: VS Code colors `const`-bound variables blue via semantic tokens and leaves mutable ones whitish. The base theme uses whitish for all; the semantic-token rules above restore the blue where a language server proves const-ness.

## Changelog from the original `igor570/zed-dark-2026`

This fork was created because the upstream `igor570/zed-dark-2026` was effectively unmaintained — a single initial commit, no response to issues, colors that drifted from the VS Code 2026-dark reference. The fork was created on 2026-06-05 and the upstream was switched to a read-only `upstream` remote. Every change below is relative to `7f71516` (upstream HEAD).

### Color corrections from VS Code 2026-dark

Verified against `/opt/visual-studio-code/resources/app/extensions/theme-defaults/themes/2026-dark.json` (the running VS Code 1.113 install), not the GitHub mirror. The 2026-dark include chain is `2026-dark` → `dark_modern` → `dark_plus` → `dark_vs`; the **parent's rules win** because they're processed last.

| Key | Upstream | This fork | VS Code 2026-dark |
|---|---|---|---|
| `background` | `#191a1b` | `#121314` | `editor.background` |
| `border.disabled` | `#2a2b2c` | `#8384854D` | `editorIndentGuide.background` |
| `element.background` | `#242526` | `#202122` | `editorWidget.background` |
| `element.selected` | `#3994bc26` | `#2C2D2E` | `list.inactiveSelectionBackground` |
| `text` | `#bfbfbf` | `#BBBEBF` | `editor.foreground` |
| `text.placeholder` | `#555555` | `#858889` | `editorLineNumber.foreground` |
| `text.disabled` | `#555555` | `#858889` | `editorLineNumber.foreground` |
| `icon` | `#bfbfbf` | `#8C8C8C` | `icon.foreground` |
| `icon.disabled` | `#555555` | `#8C8C8C` | `icon.foreground` |
| `success` | `#73c991` | `#72C892` | `editorGutter.addedBackground` |

### Corrections from the Zed source (`crates/theme/src/fallback_themes.rs`)

These were **wrong in the previous commit** and reverted after cross-referencing the One Dark fallback that ships with Zed. The fallback makes it explicit that `border.transparent` and `element.disabled` are **supposed to be transparent** (placeholder borders, disabled-state overlays) — the previous commit had set them to solid colors.

| Key | Wrong | Correct | Why |
|---|---|---|---|
| `border.transparent` | `#838485` | `#00000000` | Fallback: `SystemColors::default().transparent`. Documented as "Used for placeholder borders when an element gains a border on state change." |
| `element.disabled` | `#2C2D2E` | `#00000000` | Fallback: `SystemColors::default().transparent`. Documented as the disabled-state background of a button/input, which Zed draws on top of the parent's background. |

### 66 missing schema keys added

The upstream theme had **121 style keys**. The Zed schema (`crates/theme/src/styles/colors.rs`, `status.rs`) defines **187 keys**. The 66 missing ones were filled in with values from either VS Code 2026-dark or the One Dark fallback:

**VS Code 2026-dark (have direct equivalents):**
- `editor.indent_guide` → `editorIndentGuide.background` (`#8384854D`)
- `editor.indent_guide_active` → `editorIndentGuide.activeBackground` (`#838485`)
- `editor.hover_line_number` → `editorLineNumber.foreground` (`#858889`)
- `editor.debugger_active_line.background` → `debugEditor.activeLineBackground` (`#2A2B2CFF`)
- `editor.diff_hunk.added.background` → `diffEditor.insertedTextBackground` (`#347D3926`)
- `editor.diff_hunk.added_hollow_border` → `diffEditor.insertedTextBorder` (`#347D39`)
- `editor.diff_hunk.deleted.background` → `diffEditor.removedTextBackground` (`#C93C3726`)
- `editor.diff_hunk.deleted_hollow_border` → `diffEditor.removedTextBorder` (`#C93C37`)
- `search.active_match_background` → `editor.findMatchHighlightBackground` (`#6199BF80`)
- `element.selection_background` → `editor.selectionBackground` (`#27678280`)

**One Dark fallback (Zed-specific, no VS Code equivalent):**
- `debugger_accent` — breakpoint line color
- `drop_target.border` — border around a drop target
- `panel.indent_guide`, `panel.indent_guide_hover`, `panel.indent_guide_active` — 3 keys
- `panel.overlay_background`, `panel.overlay_hover` — 2 keys
- `pane.focused_border` — focused pane border
- `scrollbar.thumb.active_background` — scrollbar drag color
- `minimap.thumb.{background,hover_background,active_background,border}` — 4 keys
- `version_control.{added,deleted,modified,renamed,conflict,ignored}` — 6 keys
- `version_control.{word_added,word_deleted,conflict_marker.ours,conflict_marker.theirs}` — 4 keys
- `terminal.ansi.dim_{black,red,green,yellow,blue,magenta,cyan,white}` — 8 keys
- `terminal.ansi.background` — terminal ANSI background
- `vim.normal_background`, `vim.insert_background`, `vim.replace_background`, `vim.visual_background`, `vim.visual_line_background`, `vim.visual_block_background`, `vim.yank_background` — 7 keys
- `vim.helix_normal_background`, `vim.helix_select_background` — 2 keys
- `vim.normal.foreground`, `vim.insert.foreground`, `vim.replace.foreground`, `vim.visual.foreground`, `vim.visual_line.foreground`, `vim.visual_block.foreground` — 6 keys
- `vim.helix_jump_label.foreground`, `vim.helix_normal.foreground`, `vim.helix_select.foreground` — 3 keys
- `predictive.background`, `predictive.border` — 2 keys
- `unreachable.background`, `unreachable.border` — 2 keys

**Total: 66 keys added. Final style key count: 187.**

### VSCode colorCustomizations cataloged

Every color override that a built-in or user-installed VS Code extension contributes was cataloged and checked against the theme. **26 overrides total:**

- **Built-in (11, all from `vscode.git`):** `gitDecoration.{added,modified,deleted,renamed,untracked,ignored,stageModified,stageDeleted,conflicting,submodule}ResourceForeground` and `git.blame.editorDecorationForeground`. Only the 8 `gitDecoration.*ResourceForeground` colors are defined in 2026-dark; the theme's `created`/`modified`/`deleted`/`renamed`/`conflict`/`ignored` status colors already match.
- **User-installed (15):** `codeforces.{newbie,…,legendaryGrandmaster}` (10, all rank colors — extension-specific, not in 2026-dark), `testExplorer.errorDecorationBackground`, `clangd.inactiveRegions.background`, `rainbowtrack{1,2,3}` (3, CSV track colors). All extension-specific, not in 2026-dark.

### Summary

| Metric | Upstream | This fork |
|---|---|---|
| Commits | 1 | 2 |
| Style keys | 121 | 187 |
| Colors matching VS Code 2026-dark (in mapped keys) | partial | yes |
| Schema completeness | 65% | 100% |
| Maintained | no | yes |

## Credits

Derived from [VS Code's theme-defaults](https://github.com/microsoft/vscode/tree/main/extensions/theme-defaults) (MIT, © Microsoft Corporation). Not affiliated with or endorsed by Microsoft.

Forked from `igor570/zed-dark-2026` (MIT). The original single-commit import provided a working starting point; the schema, color, and correctness work in this fork is not the original author's.
