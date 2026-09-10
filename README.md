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

The engine lives in **[offline-sync-engine](https://github.com/chinmay-tayade/offline-sync-engine)**
(9 tests, standalone); it gets wired into **[argent-android](https://github.com/chinmay-tayade/argent-android)** behind WorkManager.

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

Built in **[basis-kmp](https://github.com/chinmay-tayade/basis-kmp)** — shared domain + `SHARING.md` done, Android app runs, iOS framework links.

</details>

<!-- ────────────────────────────────────────────────────────────────────────── -->

## &nbsp;Featured work

### &nbsp;&nbsp;Flagships &nbsp;·&nbsp; where most of the effort goes

<table>
<tr>
<td width="33.33%" valign="top">

**🏦 &nbsp;[argent&#8209;android](https://github.com/chinmay-tayade/argent-android)**

<sub>*Retail digital banking · offline&#8209;first*</sub>

Multi&#8209;module Kotlin/Compose. Foundation shipped — convention plugins, a
currency&#8209;safe domain layer with tests, Hilt&#8209;wired app, green CI.
The offline transfer state machine is next.

<kbd>kotlin</kbd> <kbd>compose</kbd> <kbd>multi&#8209;module</kbd> <kbd>offline&#8209;first</kbd> <kbd>fintech</kbd>

</td>
<td width="33.33%" valign="top">

**🧩 &nbsp;[basis&#8209;kmp](https://github.com/chinmay-tayade/basis-kmp)**

<sub>*Shared Kotlin core · Android + iOS*</sub>

One financial core — portfolio valuation, cost basis, allocation — shared via
Koin, with fully native Compose and SwiftUI UIs. Shared domain + tests done,
Android app runs, iOS framework links. Ships **`SHARING.md`**: the
module&#8209;by&#8209;module share&#8209;vs&#8209;native call.

<kbd>kmp</kbd> <kbd>compose</kbd> <kbd>swiftui</kbd> <kbd>koin</kbd> <kbd>ios</kbd>

</td>
<td width="33.33%" valign="top">

**📒 &nbsp;[ledger&#8209;core](https://github.com/chinmay-tayade/ledger-core)**

<sub>*Double&#8209;entry ledger · KMP library*</sub>

Pure Kotlin: balanced journal entries, idempotent postings, a `Money` type
with currency safety. The fintech fundamentals — 12 tests, JVM + iOS, green CI.

<kbd>kmp</kbd> <kbd>fintech</kbd> <kbd>ledger</kbd> <kbd>library</kbd>

</td>
</tr>
</table>

### &nbsp;&nbsp;Also building &nbsp;·&nbsp; focused supporting repos

<table>
<tr>
<td width="50%" valign="top">

**💳 &nbsp;[pay&#8209;sheet](https://github.com/chinmay-tayade/pay-sheet)** &nbsp;<sub>*checkout / payment&#8209;sheet module*</sub>

Card input with Luhn + brand detection, tokenization, a 3&#8209;D&#8209;Secure&#8209;style
step&#8209;up state machine, PCI&#8209;conscious notes. 11 tests, sample app.

<kbd>android</kbd> <kbd>compose</kbd> <kbd>payments</kbd> <kbd>3ds</kbd>

</td>
<td width="50%" valign="top">

**🔄 &nbsp;[offline&#8209;sync&#8209;engine](https://github.com/chinmay-tayade/offline-sync-engine)** &nbsp;<sub>*standalone sync library*</sub>

Durable operation queue, exponential backoff + full jitter, pluggable
per&#8209;operation conflict strategies. 9 tests, green CI.

<kbd>offline&#8209;first</kbd> <kbd>workmanager</kbd> <kbd>library</kbd>

</td>
</tr>
<tr>
<td width="50%" valign="top">

**🏗️ &nbsp;[modulith](https://github.com/chinmay-tayade/modulith)** &nbsp;<sub>*Android architecture template*</sub>

Gradle convention plugins + a `checkModuleGraph` task that fails the build on
module&#8209;graph violations — verified catching a `core → feature` edge.

<kbd>gradle</kbd> <kbd>convention&#8209;plugins</kbd> <kbd>architecture</kbd>

</td>
<td width="50%" valign="top">

**⚡ &nbsp;android&#8209;perf&#8209;lab** &nbsp;<sub>*measured performance — queued*</sub>

Baseline Profiles + Macrobenchmark against argent&#8209;android — real
before/after with device + iteration count. Sequenced after argent has real
screens worth measuring.

<kbd>performance</kbd> <kbd>baseline&#8209;profiles</kbd> <kbd>macrobenchmark</kbd>

</td>
</tr>
</table>

> Each is built the same way: scaffold → `./gradlew build` green → feature
> commits → tests → CI → README. Honest history, no big&#8209;bang dumps.

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
