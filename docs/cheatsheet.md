<!-- SPDX-License-Identifier: LGPL-3.0-only -->
<!-- SPDX-FileCopyrightText: Copyright (C) 2026 mplx <mplx@jennifer-lang.dev> -->

# texttransform cheatsheet

Every export of `@skiffyard/texttransform`. Guides are listed in
[the introduction](index.md).

```jennifer
import "@skiffyard/texttransform/" as texttransform;
```

A bad spec or style throws `Error{kind: "texttransform"}`.

## Constants

| Constant | Value | Meaning |
|---|---|---|
| `ERROR_KIND` | `"texttransform"` | the kind of every error |
| `ELLIPSIS` | `"..."` | what `truncate=` puts at the end of a cut text |
| `MODIFIERS` | `["case", "slice", "truncate", "trim", "squash", "fold", "reverse"]` | the modifiers a spec may use |
| `CASES` | `["upper", "lower", "title", "sentence", "snake", "kebab", "camel", "pascal", "constant", "dot", "path", "swap", "leet"]` | the values `case=` takes |

## Functions

| Function | Returns | Does |
|---|---|---|
| `texttransform.t(text as string, spec as string)` | `string` | apply a `\|`-separated spec, left to right |
| `texttransform.toCase(text as string, style as string)` | `string` | one case conversion |
| `texttransform.words(text as string)` | `list of string` | the words the case conversions see |
