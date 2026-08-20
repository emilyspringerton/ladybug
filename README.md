# ladybug

EINHORN_INDUSTRIAL's official BDD testing framework for [PARENA](https://github.com/emilyspringerton/PARENA)
— a real, direct port of the shape Go's own Ginkgo/Gomega pair, published as its own standalone
repo the same way Ginkgo and Gomega are their own repos rather than bundled into the Go toolchain.

## The beetle-naming lineage

Every piece here is named after a real beetle:

| File | Role | Beetle | Why |
|---|---|---|---|
| `firefly.prn` | `testing.T`-equivalent (`errorf`/`fatalf`/`skip`/`run-tests`) | Firefly (*Lampyridae*) | fireflies are real beetles; they "illuminate" test results |
| `firefly/ladybug.prn` | Gomega-equivalent matcher chain (`expect(x).to(equal(y))`) | Ladybug / ladybird (*Coccinellidae*) | a real beetle, and a direct pun — a *ladybug*-family library is the thing that finds *bugs* |
| `firefly/gomega.prn` | back-compat alias of `firefly/ladybug.prn` | — | kept so existing `(import firefly/gomega)` call sites don't break; delegates to `ladybug`, same exported API |
| `scarab.prn` | Ginkgo-equivalent runner (`describe`/`context`/`it`/`before-each`/`after-each`/`run-suite`) | Scarab beetle | scarabs' own real mythological cycle/renewal association — a runner that cycles through the suite on every run |

`gomega` itself broke this convention (it's Go's own library name, not a beetle) — this repo's
naming was corrected to `ladybug` for exactly that reason, with `firefly/gomega.prn` kept in place
purely as a compatibility shim, not deleted.

## Honest current status

**Source-published; does not yet compile end-to-end against the current PARENA (VS0) compiler.**
All four files here pass VS0 domain 1 (`parena parse`) and domain 2 (`parena analyze`, the region
analyzer) cleanly today — verified as real, current Bazel targets in `BUILD.bazel`/CI. Domain 3
(`parena build`, the C emitter) is genuinely blocked, on real, pre-existing, tracked PARENA gaps:

1. **`Vec` as a generic field/collection type isn't resolved yet** (`firefly.prn`'s own
   `TestReport`/`T` structs use `(Vec String) @ Region`) — blocks `firefly.prn` first, before any
   of the below are even reached.
2. **Reference types (`&Any`, `&mut T`, `&Expectation`, etc.) aren't supported as parameter, field,
   or return types** — blocks `firefly.prn` (`&mut T` params), `firefly/ladybug.prn` and
   `firefly/gomega.prn` (`&Any`, `&mut T`, `&Expectation`), and `scarab.prn` (`&mut T`, `deref`).
3. **Non-zero-argument, typed `Fn` callback parameters aren't supported** — VS0 currently only
   understands zero-argument `(Fn [] <ReturnType>)`; the matcher-chain shape here needs
   `(Fn [&Any] Bool)` (`to`'s matcher argument, `equal`/`be-true`/`be-nil`'s own return types) and
   `(Fn [&mut T] Unit)` (`scarab.prn`'s spec bodies).
4. **`defenum` variants are capped at one payload field** — blocks `scarab.prn`'s own `SuiteNode`
   (`Group` needs two: `name` and `children`).

These are the same real gaps tracked in [PARENA/STDLIB.md](https://github.com/emilyspringerton/PARENA/blob/main/STDLIB.md)'s
own gap-analysis section, not something invented fresh for this repo. As each closes in PARENA's
own emitter, this repo's CI gains a real `parena build` step for the newly-unblocked file(s) — not
before, and not faked in the meantime.

## Building

```bash
bazel build //:firefly_verify //:ladybug_verify //:gomega_alias_verify //:scarab_verify
```

Pulls in a pinned PARENA commit via `MODULE.bazel`'s own `git_override` (not a floating branch —
same deterministic-build discipline PARENA's own CI holds itself to).

## License

Public domain (Unlicense) — same as PARENA itself.
