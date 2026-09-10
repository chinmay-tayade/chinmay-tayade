# Chinmay Tayade

**Mobile engineer — Android · Kotlin · Jetpack Compose · Kotlin Multiplatform**

I build production-grade mobile systems: offline-first architecture, secure
storage, multi-module scale, and shared Kotlin cores across Android and iOS.
IIIT Allahabad.

[LinkedIn](https://www.linkedin.com/in/chinmaytayade) · chinmaytayade@outlook.com

---

## What I work on

- **Android at scale** — multi-module architecture with enforced dependency
  boundaries, Compose, coroutines/Flow, Hilt, Room, WorkManager, Paging.
- **Kotlin Multiplatform** — sharing domain, data and networking across
  Android and iOS while keeping UI native. I care about *what not to share*.
- **Offline-first & sync** — operation queues, optimistic UI, conflict
  resolution, exponential backoff, eventual-consistency UX.
- **Mobile security** — Keystore / Keychain-backed encryption, BiometricPrompt
  with CryptoObject, certificate pinning, session hardening.
- **Performance & quality** — Baseline Profiles, Macrobenchmark, Compose
  recomposition work, and a testing pyramid that holds weight.

## Projects

### [argent-android](https://github.com/chinmay-tayade/argent-android) &nbsp;·&nbsp; digital banking, offline-first &nbsp;·&nbsp; _in progress_

Multi-module Kotlin / Compose banking app built around the hard parts of a
payments client: an offline transfer state machine (idempotency keys,
optimistic UI, per-operation conflict resolution), background sync via
WorkManager, biometric-gated Keystore encryption, certificate pinning,
Baseline Profiles, full CI. Runs end-to-end against an in-repo mock server.

`kotlin` · `compose` · `multi-module` · `offline-first` · `mvi` · `fintech` · `mobile-security`

### basis-kmp &nbsp;·&nbsp; shared Kotlin core, Android + iOS &nbsp;·&nbsp; _planned_

Investment tracker with a shared domain / data / network layer (Ktor,
SQLDelight, Koin) and fully native UIs — Jetpack Compose and SwiftUI. Ships
with `SHARING.md`: a module-by-module rationale for what is shared versus
deliberately kept native.

### modulith &nbsp;·&nbsp; opinionated Android architecture template &nbsp;·&nbsp; _planned_

Gradle convention plugins, a CI check that fails builds on module-graph
violations, wired-in benchmark + baseline-profile setup, and a non-trivial
sample app.

## How I think about engineering

Design for the failure case first. Share logic, not UI. Measure before
claiming a win. A test suite you trust is worth more than one that's just
green. Boring, well-understood tools over novel ones — until the novel one
earns its place.

## Currently going deeper on

Compose Multiplatform · Kotlin/Native performance · on-device inference
