# kotoba-lang/render-shaders

Kotoba package for `kotoba.render-shaders`.

## Dependency change (2026-07-09)

`kotoba.render-shaders` now requires `kami.wgsl` (from `kotoba-lang/webgpu`) directly instead of
the standalone `kotoba-lang/wgsl` repo's `kotoba.wgsl`. The two copies of the WGSL-as-data compiler
were byte-identical (no behavioural bug, unlike the `sprite-gpu` case), but only `kami.wgsl`
receives ongoing feature/bugfix work — see `kotoba-lang/wgsl`'s CHANGELOG.md for the full writeup.
`kotoba-lang/wgsl` itself now re-exports `kami.wgsl` too, so this change is behaviour-preserving
either way — it just removes an extra hop.

## Test

```sh
clojure -M:test
```
