<!-- ────────────────────────────────────────────────────────────────────────── -->

<div align="center">

<img src="https://raw.githubusercontent.com/chinmay-tayade/chinmay-tayade/main/assets/header.svg" alt="Chinmay Tayade — Mobile Engineer · Android · Kotlin · Jetpack Compose · Kotlin Multiplatform" width="100%"/>

<br/>

<a href="https://www.linkedin.com/in/chinmaytayade"><img src="https://img.shields.io/badge/LinkedIn-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white" alt="LinkedIn"/></a>&nbsp;
<a href="mailto:chinmaytayade@outlook.com"><img src="https://img.shields.io/badge/Email-D14836?style=for-the-badge&logo=maildotru&logoColor=white" alt="Email"/></a>&nbsp;
<img src="https://komarev.com/ghpvc/?username=chinmay-tayade&label=Profile%20views&color=8b5cf6&style=for-the-badge" alt="Profile views"/>

<br/><br/>

<img src="https://readme-typing-svg.demolab.com?font=JetBrains+Mono&weight=600&size=20&pause=1400&color=A855F7&center=true&vCenter=true&width=740&lines=I+design+for+the+failure+case+first.;Offline-first+sync+%E2%80%94+what+happens+with+no+signal%3F;KMP+%E2%80%94+share+the+logic%2C+keep+the+UI+native.;Secure+by+design+%E2%80%94+Keystore%2C+biometrics%2C+pinning.;Measure+before+claiming+a+win." alt="Positioning" />

</div>

<!-- ────────────────────────────────────────────────────────────────────────── -->

## &nbsp;`whoami`

Mobile engineer building **B2C fintech-grade** Android apps — the kind where a
dropped connection mid-transfer, a stolen unlocked phone, or a schema migration
gone wrong are *design inputs*, not afterthoughts. Primary stack: **Android ·
Kotlin · Jetpack Compose · Kotlin Multiplatform**. IIIT Allahabad.

```kotlin
object Chinmay {
    val core       = listOf("Android", "Kotlin", "Jetpack Compose", "KMP")
    val breadth    = listOf("iOS / SwiftUI", "Flutter", "React Native")
    val domain     = listOf("digital banking", "payments", "ledgers", "KYC onboarding")
    val obsessions = listOf(
        "what NOT to share in KMP",
        "eventual consistency as a UX contract",
        "a test suite you actually trust",
    )
    val default    = "boring, well-understood tools — until the novel one earns its place"
}
```

<!-- ────────────────────────────────────────────────────────────────────────── -->

## &nbsp;The signature problem: a transfer you don't fully control

The user taps **Send** with no signal. What does a serious banking client do?
Not spin. Not lie. It persists the intent, shows the truth, and reconciles later.

<details>
<summary><b>Open the offline transfer state machine</b></summary>

<br/>

```mermaid
stateDiagram-v2
    direction LR
    [*] --> PENDING: submit · local validation passes · opId assigned
    PENDING --> SYNCING: worker picks it up · network available
    SYNCING --> CONFIRMED: 2xx · server id returned
    SYNCING --> CONFLICT: 409 · needs resolution
    SYNCING --> FAILED: 4xx · terminal reason
    SYNCING --> PENDING: 5xx / IO · backoff + full jitter · attempts < ceiling
    SYNCING --> FAILED: attempts == ceiling
    CONFLICT --> PENDING: user re-applies
    CONFLICT --> FAILED: user cancels
    CONFIRMED --> [*]
    FAILED --> [*]
```

- **Idempotency key** (`opId`, client-generated) persisted *before* any network
  attempt → a retried request the server already saw returns the original result.
- **Optimistic UI** — the transfer appears instantly as `PENDING`, de-emphasised,
  with retry / cancel. The app never claims success it can't verify.
- **Backoff** is exponential with *full jitter* and a ceiling — so every device
  that went offline in an outage doesn't stampede the server when it returns.
- **Conflict resolution is per operation type**, not one global rule.

Built out in **[argent-android](https://github.com/chinmay-tayade/argent-android)**,
then extracted into a standalone `offline-sync-engine`.

</details>

<!-- ────────────────────────────────────────────────────────────────────────── -->

## &nbsp;Kotlin Multiplatform: share the logic, keep the UI native

<details>
<summary><b>Open: what I share vs. what I deliberately keep native</b></summary>

<br/>

```mermaid
flowchart TD
    subgraph AND["📱 Android"]
        A1["Jetpack Compose<br/>Material 3 · Vico charts"]
        A2["ViewModel · collectAsState"]
    end
    subgraph IOS["🍎 iOS"]
        I1["SwiftUI<br/>Swift Charts"]
        I2["ObservableObject · SKIE · async/await"]
    end
    subgraph SHARED["🟣 shared — Kotlin Multiplatform"]
        S1["domain — portfolio math, validation, business rules"]
        S2["data — repositories, sync, DTO ↔ domain mappers"]
        S3["network — Ktor + serialization"]
        S4["persistence — SQLDelight"]
    end
    A2 --> S1
    I2 --> S1
    S1 --> S2 --> S3
    S2 --> S4
    A1 -. expect/actual .-> SEC["biometrics · secure storage"]
    I1 -. expect/actual .-> SEC
```

**Shared:** everything that's expensive to get wrong twice — domain logic, data,
networking, persistence. One implementation, one test suite, run on JVM *and*
Kotlin/Native. **Native:** all UI, navigation, charts, notifications, biometric
prompts — the things users actually feel. The decision, module by module, is
written up in `SHARING.md`.

Built in **basis-kmp** *(next up)*.

</details>

<!-- ────────────────────────────────────────────────────────────────────────── -->

## &nbsp;Featured work

<table>
<tr>
<th align="left" width="210">Repo</th><th align="left">What it demonstrates</th><th align="left" width="90">Status</th>
</tr>

<tr><td valign="top">

**[argent-android](https://github.com/chinmay-tayade/argent-android)**
<br/><sub>digital banking</sub>

</td><td valign="top">

Multi-module Kotlin/Compose retail banking app. Foundation shipped —
convention plugins, a currency-safe domain layer with tests, Hilt-wired app,
green CI. Building toward the offline transfer state machine, biometric
Keystore encryption, cert pinning and Baseline Profiles ([roadmap](https://github.com/chinmay-tayade/argent-android/blob/main/ROADMAP.md)).

<sub>`kotlin` `compose` `multi-module` `offline-first` `mvi` `fintech` `mobile-security`</sub>

</td><td valign="top"><br/>🟢 building</td></tr>

<tr><td valign="top">

**basis-kmp**
<br/><sub>KMP · Android + iOS</sub>

</td><td valign="top">

Shared Kotlin financial core (portfolio valuation, cost basis, allocation) across
Android and iOS, with fully native Compose and SwiftUI UIs. Ships `SHARING.md` —
the module-by-module share-vs-native rationale.

<sub>`kotlin-multiplatform` `compose` `swiftui` `ktor` `sqldelight` `koin`</sub>

</td><td valign="top"><br/>⚪ next</td></tr>

<tr><td valign="top">

**ledger-core**
<br/><sub>KMP library</sub>

</td><td valign="top">

A pure-Kotlin **double-entry ledger**: accounts, postings, immutable transactions,
idempotency, a `Money` type with correct rounding and currency safety. The fintech
fundamentals, heavily tested, multiplatform.

<sub>`kotlin-multiplatform` `fintech` `ledger` `double-entry` `library`</sub>

</td><td valign="top"><br/>⚪ planned</td></tr>

<tr><td valign="top">

**pay-sheet**
<br/><sub>payments module</sub>

</td><td valign="top">

A drop-in Compose **checkout / payment-sheet** module: card input with Luhn +
network detection, tokenization flow, 3-D-Secure-style step-up, PCI-conscious
design notes. Small, focused, production-shaped.

<sub>`android` `compose` `payments` `checkout` `3ds`</sub>

</td><td valign="top"><br/>⚪ planned</td></tr>

<tr><td valign="top">

**offline-sync-engine**
<br/><sub>library</sub>

</td><td valign="top">

The sync core from `argent-android`, standalone and Maven-published: operation
queue, exponential backoff + jitter, pluggable conflict strategies, connectivity
observation.

<sub>`android` `offline-first` `sync` `workmanager` `library`</sub>

</td><td valign="top"><br/>⚪ planned</td></tr>

<tr><td valign="top">

**modulith**
<br/><sub>architecture template</sub>

</td><td valign="top">

Opinionated Android architecture: Gradle convention plugins, a CI check that fails
builds on module-graph violations, wired-in benchmark + baseline-profile setup,
demonstrated with a non-trivial sample app.

<sub>`android` `gradle` `convention-plugins` `architecture` `ci`</sub>

</td><td valign="top"><br/>⚪ planned</td></tr>

<tr><td valign="top">

**android-perf-lab**
<br/><sub>performance</sub>

</td><td valign="top">

Baseline Profiles + Macrobenchmark run against `argent-android`. Real numbers,
real methodology, real before/after — every measurement carries its device,
build type and iteration count.

<sub>`android` `performance` `baseline-profiles` `macrobenchmark`</sub>

</td><td valign="top"><br/>⚪ planned</td></tr>

</table>

> Then, one at a time, we build each: scaffold → local `./gradlew build` green →
> feature commits → tests → CI → README. Honest history, no big-bang dumps.

<!-- ────────────────────────────────────────────────────────────────────────── -->

## &nbsp;Toolbox

<p>
<img src="https://img.shields.io/badge/Kotlin-7F52FF?style=flat-square&logo=kotlin&logoColor=white"/>
<img src="https://img.shields.io/badge/Swift-F05138?style=flat-square&logo=swift&logoColor=white"/>
<img src="https://img.shields.io/badge/Dart-0175C2?style=flat-square&logo=dart&logoColor=white"/>
<img src="https://img.shields.io/badge/TypeScript-3178C6?style=flat-square&logo=typescript&logoColor=white"/>
&nbsp;
<img src="https://img.shields.io/badge/Jetpack_Compose-4285F4?style=flat-square&logo=jetpackcompose&logoColor=white"/>
<img src="https://img.shields.io/badge/Kotlin_Multiplatform-7F52FF?style=flat-square&logo=kotlin&logoColor=white"/>
<img src="https://img.shields.io/badge/SwiftUI-0071E3?style=flat-square&logo=swift&logoColor=white"/>
<img src="https://img.shields.io/badge/Material_3-757575?style=flat-square&logo=materialdesign&logoColor=white"/>
</p>
<p>
<img src="https://img.shields.io/badge/Coroutines_%2F_Flow-7F52FF?style=flat-square&logo=kotlin&logoColor=white"/>
<img src="https://img.shields.io/badge/Hilt-2196F3?style=flat-square&logo=android&logoColor=white"/>
<img src="https://img.shields.io/badge/Koin-EA4C89?style=flat-square"/>
<img src="https://img.shields.io/badge/Room-3DDC84?style=flat-square&logo=android&logoColor=white"/>
<img src="https://img.shields.io/badge/SQLDelight-005571?style=flat-square&logo=sqlite&logoColor=white"/>
<img src="https://img.shields.io/badge/WorkManager-3DDC84?style=flat-square&logo=android&logoColor=white"/>
<img src="https://img.shields.io/badge/Paging_3-3DDC84?style=flat-square&logo=android&logoColor=white"/>
&nbsp;
<img src="https://img.shields.io/badge/Retrofit-48B983?style=flat-square&logo=square&logoColor=white"/>
<img src="https://img.shields.io/badge/OkHttp-48B983?style=flat-square&logo=square&logoColor=white"/>
<img src="https://img.shields.io/badge/Ktor-087CFA?style=flat-square&logo=ktor&logoColor=white"/>
</p>
<p>
<img src="https://img.shields.io/badge/JUnit-25A162?style=flat-square&logo=junit5&logoColor=white"/>
<img src="https://img.shields.io/badge/MockK-EE0000?style=flat-square"/>
<img src="https://img.shields.io/badge/Turbine-48B983?style=flat-square&logo=square&logoColor=white"/>
<img src="https://img.shields.io/badge/Macrobenchmark-3DDC84?style=flat-square&logo=android&logoColor=white"/>
&nbsp;
<img src="https://img.shields.io/badge/Gradle-02303A?style=flat-square&logo=gradle&logoColor=white"/>
<img src="https://img.shields.io/badge/GitHub_Actions-2088FF?style=flat-square&logo=githubactions&logoColor=white"/>
<img src="https://img.shields.io/badge/Firebase-DD2C00?style=flat-square&logo=firebase&logoColor=white"/>
</p>

<!-- ────────────────────────────────────────────────────────────────────────── -->

## &nbsp;How I think about engineering

| Principle | In practice |
|---|---|
| **Design for the failure case first** | No signal, stolen device, mid-migration — that's where the architecture is decided |
| **Share logic, not UI** | KMP: domain + data shared, UI native. Knowing *what not to share* is the skill |
| **Measure before claiming a win** | A perf number ships with its device, build type and iteration count — or it doesn't ship |
| **Trust the test suite** | Hand-written fakes over brittle mocks; cover the logic that moves money |
| **Boring by default** | Proven tools until a new one earns its place |

<!-- ────────────────────────────────────────────────────────────────────────── -->

<details>
<summary>&nbsp;<b>GitHub activity</b></summary>

<br/>

<div align="center">

<img height="160" src="https://github-readme-stats.vercel.app/api?username=chinmay-tayade&show_icons=true&hide_border=true&count_private=true&include_all_commits=true&bg_color=0d1117&title_color=a855f7&icon_color=a855f7&text_color=c9d1d9" alt="stats"/>
<img height="160" src="https://github-readme-stats.vercel.app/api/top-langs/?username=chinmay-tayade&layout=compact&hide_border=true&langs_count=8&bg_color=0d1117&title_color=a855f7&text_color=c9d1d9" alt="languages"/>

</div>

</details>

<br/>

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/chinmay-tayade/chinmay-tayade/output/github-contribution-grid-snake-dark.svg"/>
  <img src="https://raw.githubusercontent.com/chinmay-tayade/chinmay-tayade/output/github-contribution-grid-snake.svg" alt="contribution snake" width="100%"/>
</picture>

<div align="center">
<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=12,20,24,30&height=90&section=footer" width="100%"/>
</div>
