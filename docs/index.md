<!-- SPDX-License-Identifier: LGPL-3.0-only -->
<!-- SPDX-FileCopyrightText: Copyright (C) 2026 mplx <mplx@jennifer-lang.dev> -->

# @skiffyard/texttransform

Text transformations in the style of `printf`'s modifiers: case
conversions (snake, kebab, camel, pascal, constant, leet, ...), slicing,
truncation, trimming. A spec is a `|`-separated list, applied left to
right, so `"trim|fold|case=kebab"` turns `"  Crème Brûlée  "` into
`"creme-brulee"`.

This deck is part of [Skiffyard](https://github.com/skiffyard), small community decks for [Jennifer](https://jennifer-lang.dev/).

## Why a function and not a printf verb

`printf`'s modifiers shape how a value is *printed* (`%d|base=2` writes
5 as `101`) and never change the value. Transformations that do
(`case=upper`, `slice=0:5`) were deliberately kept out of the format
language. This deck brings them back as `t(text, spec)`, which composes
with the real `io.sprintf`: the format string keeps every verb and
presentation modifier on its typed arguments, and the transformed parts
are plain strings.

```jennifer
import "@skiffyard/texttransform/" as texttransform;

io.printf("%s=%d\n", texttransform.t("max retries", "case=constant"), 3);   # MAX_RETRIES=3
io.printf("[%s]\n", texttransform.t($title, "squash|truncate=30"));
```

A user function cannot take a variable number of arguments of different
types, so a deck cannot offer its own `xprintf`; transforming each value
with `t` before `sprintf` is the composable form.

## Modifiers

| Modifier | Does |
|---|---|
| `case=STYLE` | one of the case styles below |
| `slice=START:END` | characters START to END, half-open; either side may be empty, negative counts from the end |
| `truncate=N` | at most N characters, ending in `...` when cut |
| `trim` | leading and trailing whitespace removed; `trim=left` only leading, `trim=right` only trailing |
| `squash` | every whitespace run one space, trimmed |
| `fold` | Latin diacritics and ligatures folded (`é` to `e`, `æ` to `ae`) |
| `reverse` | the characters in reverse order |

## Case styles

For `"jenniferDecks parseJSONFiles v2Fast"`:

| Style | Result |
|---|---|
| `upper` | `JENNIFERDECKS PARSEJSONFILES V2FAST` |
| `lower` | `jenniferdecks parsejsonfiles v2fast` |
| `title` | `Jenniferdecks Parsejsonfiles V2fast` |
| `sentence` | `Jenniferdecks parsejsonfiles v2fast` |
| `snake` | `jennifer_decks_parse_json_files_v2_fast` |
| `kebab` | `jennifer-decks-parse-json-files-v2-fast` |
| `camel` | `jenniferDecksParseJsonFilesV2Fast` |
| `pascal` | `JenniferDecksParseJsonFilesV2Fast` |
| `constant` | `JENNIFER_DECKS_PARSE_JSON_FILES_V2_FAST` |
| `dot` | `jennifer.decks.parse.json.files.v2.fast` |
| `path` | `jennifer/decks/parse/json/files/v2/fast` |
| `swap` | `JENNIFERdECKS PARSEjsonfILES V2fAST` |
| `leet` | `j3nn1f3rD3ck5 p4r53J50NF1l35 v2F457` |

`upper`, `lower`, `title`, `sentence`, `swap` and `leet` keep the text's
characters and spacing. The joined styles (`snake` to `path`) rebuild
the text from its words, which `words` shows: anything that is neither a
letter nor a digit separates words, and a word also ends where a lower-case
letter or a digit meets an upper-case one (`fooBar`, `v2Go`) and where an
upper-case run meets a capitalised word (`HTTPServer` is `HTTP` and
`Server`). Digits stay with the word before them. Letters of any script
count; a script without case (`漢字`) is one word per run.

`leet` replaces a, b, e, g, i, o, s, t and z (either case) with 4, 8, 3,
6, 1, 0, 5, 7 and 2.

## Characters

A character is what a reader sees as one: a letter or digit together with
the combining marks after it. `cafe\u0301` (an `e` followed by a combining
acute accent, as macOS™ file names and some keyboards write `é`) is four
characters, so `slice`, `truncate` and `reverse` never strip the accent
from its letter, and `words` keeps it in its word. This is not Unicode's
full grapheme clustering: an emoji sequence or a flag is still several
characters.

## Errors

A spec is strict: an empty spec, an unknown modifier (names are case
sensitive), a value on a modifier that takes none or none where one is
needed, a malformed number, or a slice outside the text throws
`Error{kind: "texttransform"}`. Nothing is guessed.

## Trademarks

macOS™ is a trademark of Apple Inc., named here only to describe how its file
names write accents. Other product and company names may be trademarks of
their respective owners. This deck is an independent project, not affiliated
with, sponsored or endorsed by Apple Inc.
