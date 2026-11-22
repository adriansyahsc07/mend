# mend

`mend` applies an edit that an AI model wrote to a tree of text files. Models write edits in four
formats, and `mend` reads all four to one written contract (`SPEC.md` in the kit that ships this
entry): a unified diff (often with wrong line numbers), SEARCH/REPLACE blocks, the V4A patch format,
and text-editor tool calls as JSON. Every file is changed or nothing is.

    node main.ts [--format=NAME] [--check] [--strict] [PATCH]

- The working directory is the root of the tree; every path in a patch is relative to it.
- `PATCH` is the patch file, or `-` (or absent) to read the patch from standard input.
- `--format=NAME` reads one format (`unified`, `search-replace`, `v4a`, `json`); `auto`, the
  default, detects it.
- `--check` does everything but write: the same output and exit code, no file touched.
- `--strict` matches lines exactly (tier 0 only).

An edit's old lines are found at the first of three tiers where they occur: exactly, then ignoring
trailing whitespace, then ignoring leading and trailing whitespace (re-indenting the new lines to
the file's indentation at that tier). Unified hunks take the occurrence nearest their (possibly
wrong) line number; SEARCH/REPLACE blocks require exactly one occurrence; V4A chunks walk a
cursor with anchors. A failed edit changes nothing, the edits after it still run, and when any
edit failed no file is written at all.

Exit 0 lists what changed (`A`/`D`/`M`, or `R old -> new` for a move) and `ok: <n> files`;
exit 1 lists each failure and `failed: <k> of <n> edits`; exit 2 is a usage, missing-patch,
format, or parse error, one line.

## Files

- `main.ts`: the CLI. Node 22, standard library alone.
- `runner.ts`: the kit's trial runner; kept as shipped. When `MAKEFUN_ENV_URL` is set, `main.ts`
  hands the trial over to it.
- `check.ts`: runs `main.ts` on the kit's public practice set
  (`node check.ts <practice.json>`), printing every check it gets wrong and the practice score.
- `LICENSE`: MIT.

## Practice

The 100 public practice checks pass: practice score 1000 (100 of 100), identical on repeated
runs.
