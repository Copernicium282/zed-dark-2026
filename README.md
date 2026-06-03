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

## Credits

Derived from [VS Code's theme-defaults](https://github.com/microsoft/vscode/tree/main/extensions/theme-defaults) (MIT, © Microsoft Corporation). Not affiliated with or endorsed by Microsoft.
