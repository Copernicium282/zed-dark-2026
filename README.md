# Dark 2026

A custom Zed theme by **Copernicium282**, derived from [VS Code's Dark 2026](https://github.com/microsoft/vscode/tree/main/extensions/theme-defaults) default theme (shipped with VS Code 1.113).

Near-black canvas, teal types and constants, light-blue strings, warm-orange parameters, purple functions, green tags, and the signature red structural keywords.

## Install

Search for **Dark 2026** in Zed's extensions (`cmd-shift-x` / `ctrl-shift-x`), install, then pick it in the theme selector (`cmd-k cmd-t`).

## How it was built

VS Code's `2026-dark.json` inherits through a four-file chain (`dark_modern` → `dark_plus` → `dark_vs`). The base colors here were resolved by applying VS Code's TextMate scope-specificity rules across that chain. From that foundation the theme was extended with custom choices: teal replacing many blue tones for types, constants, fields, and JSON keys; all 187 Zed schema keys covered; and tailored support for rainbow brackets, ZenScript, and semantic highlighting.

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

## Rainbow brackets

The theme defines seven accent colors for bracket colorization. Enable in Zed:

```jsonc
// ~/.config/zed/settings.json
"colorize_brackets": true
```

To set per-language, override under `languages.<Name>`.

Zed's bracket colorization fires for languages whose extension ships a `brackets.scm` query file. Built-in languages with support include Rust, TypeScript, Python, Java, Go. Languages without one (e.g. ZenScript) need the file added to the extension's `languages/<name>/brackets.scm`.

## Known deviations from VS Code

Zed highlights with tree-sitter rather than TextMate grammars, so a few distinctions can't be reproduced exactly:

- **Keywords**: VS Code splits structural (red) / flow (purple) / operator-like (blue) keywords per TextMate scope. This theme maps Zed's capture groups as closely as the grammars allow; languages whose grammar exposes only a plain `@keyword` capture render all keywords red.
- **Variables**: VS Code colors `const`-bound variables blue via semantic tokens and leaves mutable ones whitish. The base theme uses whitish for all; the semantic-token rules above restore the blue where a language server proves const-ness.

## Key features

### Teal for the most-used code constructs

Types, fields, properties, constants, booleans, enums, modules, and namespaces are highlighted in teal (`#4ec9b0`) — reducing the light-blue wash common in the original and providing better visual distinction. JSON keys (via the `property` scope) render teal while string values remain light blue.

### Schema completeness

All 187 Zed theme schema keys are covered — every UI surface, editor element, terminal ANSI variant, vim mode indicator, scrollbar state, and status color has an explicit value.

### `zencode` syntax selector for ZenScript

Zed has no built-in `bracket_handler_content` capture, but ZenScript's tree-sitter grammar exposes a `bracket_handler_content` node (the text inside `<>` angle-bracket handler syntax like `<nuclearcraft:compound:2>`). The theme adds a `zencode` selector for any extension that maps `bracket_handler_content` to it.

### `field` / `property` / `variable.member` unified

Property access like `a.b.c` renders uniformly, consistent with VS Code's behavior.

## Credits

Derived from [VS Code's theme-defaults](https://github.com/microsoft/vscode/tree/main/extensions/theme-defaults) (MIT, © Microsoft Corporation). Not affiliated with or endorsed by Microsoft.

Built by **Copernicium282**.
