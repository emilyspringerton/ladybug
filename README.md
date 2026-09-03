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

**Real, updated status, 2026-09-03 (kanban priority-queue card `LB-911`, "FIX LADYBUG AND
SCARAB")** — the original 4-gap list below turned out to be stale, not currently accurate;
checked live by actually running `parena build` against each file directly, rather than trusting
the README's own prior claim:

- **`firefly.prn` now builds cleanly all the way through domain 3 (`parena build`, the real C
  emitter)** — confirmed live. The original blockers named for it (`Vec` as a generic struct
  field, `&mut T` params) have since closed in PARENA's own emitter; this file was never
  re-checked after they did, until now. Real, new `firefly_build` Bazel target added below.
- **`firefly/ladybug.prn` (and `firefly/gomega.prn`, which delegates to it) remain genuinely
  blocked — but on a DIFFERENT, more fundamental real gap than originally listed**: `equal`'s own
  body (`(fn [(actual : &Any)] (deep-eq? actual expected))`) needs a real, closed-over `expected`
  from its OWN enclosing function's scope — VS0 has no lambda/closure captures at all yet (a real,
  substantial, separate compiler feature: heap-allocated capture environments or defunctionalized
  callables, comparable in scope to adding real generics). `equal`'s own real signature
  (`(Fn [&Any] Bool)`, a non-zero-argument, typed `Fn` return type) DOES already compile — the
  original "non-zero-argument `Fn` callback parameters aren't supported" claim is also now stale.
- **`scarab.prn` remains genuinely blocked, on a real, different gap**: an `Fn` type referencing a
  bare, generic type parameter `T` (`(Fn [&mut T] Unit)`) — VS0 still has no real generic type
  parameters at all (a well-known, already-documented, monorepo-wide limitation, not new to this
  file). Real, honest note: this is the FIRST error `parena build` reports for this file — whether
  the original list's other 3 items (reference types, multi-arg `Fn`, multi-field `defenum`) have
  also since closed for `scarab.prn` specifically is genuinely unknown until the `T`-generics
  blocker closes and a next real error (or success) surfaces; not claimed either way here.

Real, deliberate scope for this pass: closing lambda captures or real generics is a genuinely
large, separate PARENA compiler undertaking, not attempted here — this update's own real,
concrete contribution is correcting the stale gap list against what's ACTUALLY true today (found
live, not assumed) and wiring the one real, newly-unblocked `parena build` step
(`firefly_build`) rather than leaving it un-wired for another unknown stretch of time.

## Building

```bash
bazel build //:firefly_verify //:firefly_build //:ladybug_verify //:gomega_alias_verify //:scarab_verify
```

Pulls in a pinned PARENA commit via `MODULE.bazel`'s own `git_override` (not a floating branch —
same deterministic-build discipline PARENA's own CI holds itself to).

## License

Public domain (Unlicense) — same as PARENA itself.
