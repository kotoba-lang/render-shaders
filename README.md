# kotoba-lang/render-shaders

Kotoba package for `kotoba.render-shaders`.

## Dependency change (2026-07-09)

`kotoba.render-shaders` now requires `kami.wgsl` (from `kotoba-lang/webgpu`) directly instead of
the standalone `kotoba-lang/wgsl` repo's `kotoba.wgsl`. The two copies of the WGSL-as-data compiler
were byte-identical (no behavioural bug, unlike the `sprite-gpu` case), but only `kami.wgsl`
receives ongoing feature/bugfix work — see `kotoba-lang/wgsl`'s CHANGELOG.md for the full writeup.
`kotoba-lang/wgsl` itself now re-exports `kami.wgsl` too, so this change is behaviour-preserving
either way — it just removes an extra hop.

## `kotoba.render-shaders` vs. `kami.render-shaders` (2026-07-09 dedup pass)

This repo's own `kotoba.render-shaders` namespace was also checked against
`kotoba-lang/webgpu`'s internal `src/kami/render_shaders.cljc`, following the same
"clj-wgsl Phase-4 incident" pattern that caused real drift for `kotoba-lang/sprite-gpu`/
`gpu`/`webgl`. **Conclusion: no code change needed here.**

- **Content**: diffing the two (normalizing `kotoba.*`→`kami.*`) found them identical except for
  docstring wording (this repo's docstring describes it as depending on `kami.wgsl`, which
  `kami.render-shaders` — already living inside `webgpu` — has no reason to say). Every function
  body, shader generator, and constant matches byte-for-byte.
- **History**: `kotoba-lang/webgpu`'s `kami.render-shaders` was built up over several commits
  2026-06-24→25 (`16e6229`…`a275ba5`, including a real bug fix — `metahuman_skin`'s uniform
  layout). This repo's copy was wiped by the Phase-4 scaffold and restored on 2026-07-02
  (`338d406`) to content that already matched webgpu's fully-developed 06-25 state — so the
  restore captured the fix; nothing here is behind.
- **Tests**: both sides have an equivalent naga-validity gate (`test/render_naga_test.clj` here,
  `test/render_naga_test.clj` in webgpu — same assertions, webgpu's adds a `run-tests` CI-gate
  tail). webgpu additionally has `test/render_shader_test.clj`, a token-equivalence gate against
  the shipping native `kami-render` `.wgsl` sources (not applicable here since this repo doesn't
  co-locate a `kami-engine` checkout).
- **Consumers**: a repo-wide `grep -rn "kotoba-lang/render-shaders" --include=deps.edn` and a
  source-level `grep` for `kotoba.render-shaders`/`kotoba\.render_shaders` requires found **zero
  external consumers** of this repo's own namespace — nothing outside this repo depends on it.

Since the content is already in sync and nothing external depends on this namespace, converting
it into a live re-export (the `sprite-gpu`/`gpu`/`webgl` pattern) would only add plumbing with no
consumer to benefit from it. This repo is safe to keep as a self-contained, currently-in-sync
duplicate; if a real external consumer of `kotoba.render-shaders` ever appears, re-evaluate
whether to convert it to a re-export at that point (or point the new consumer at
`kami.render-shaders` directly).

## Test

```sh
clojure -M:test
```
