# Gno Sentinel

- GitHub handle: **SuperSmile0426**
- Email: **superjodev426@gmail.com**
- Links:
  - Project repository: https://github.com/SuperSmile0426/gno-sentinel
  - Current v0.2 development PR: https://github.com/SuperSmile0426/gno-sentinel/pull/1
  - GitHub profile: https://github.com/SuperSmile0426

## Project summary

**Gno Sentinel** is open-source security infrastructure for Gno.land that provides Gno-native static analysis, automated realm/package scanning, and security-focused monitoring.

Its goal is to turn Gno-specific security knowledge into reusable tooling that can be integrated into local development, CI pipelines, explorers, monitoring systems, and other developer infrastructure.

The project begins with a deterministic package-aware analyzer for `.gno` source code. It is designed to evolve into a pipeline that automatically detects newly published Gno packages, retrieves their source, performs version-aware security analysis, records findings, and exposes the results through APIs, webhooks, and a lightweight dashboard.

This directly addresses an area identified by the Gno.land grants program: **real-time contract monitoring and auditing**.

A working prototype already exists. The current v0.2 implementation includes:

- package-aware AST analysis across multiple `.gno` files;
- deterministic CLI and JSON output;
- CI-friendly severity thresholds;
- vulnerable/fixed regression fixtures;
- source excerpts and structured finding metadata;
- optional Gno-version and network context;
- four initial Gno-specific security rules;
- an ingestion abstraction designed for future tx-indexer integration.

The current rules cover:

- `GNO-PAY-001` — unsafe `OriginSend()` payment handling without a recognized `IsUserCall()` control-flow guard;
- `GNO-AUTH-001` — potentially unsafe `OriginCaller()` authorization patterns;
- `GNO-STATE-001` — exported package pointers and getters that leak mutable package-level pointer state, including cross-file cases;
- `GNO-REALM-001` — unsafe `PreviousRealm()` usage in realm-aware functions.

Gno Sentinel is not intended to replace manual security review. Its purpose is to provide fast, reproducible, ecosystem-wide detection of known Gno-specific security patterns and to create a foundation for continuous realm security monitoring.

### Goals and deliverables

The primary goal is to build an open-source security analysis layer specifically for the Gno developer ecosystem.

#### 1. Gno-native static analyzer

Expand the current prototype into a more robust analyzer capable of understanding package-wide security properties.

Deliverables:

- stable CLI and JSON finding formats;
- package-level analysis across `.gno` files;
- integration with Gno parser/type information where practical;
- version-aware rule metadata;
- deterministic finding ordering;
- source-location and evidence extraction;
- CI-compatible exit behavior;
- vulnerable and fixed regression fixtures for every rule;
- documentation explaining the security invariant behind each rule.

The first production rule set will target high-confidence Gno-specific classes such as caller identity and designation mistakes, payment validation and `OriginSend()` handling, mutable-state pointer exposure, `/p/` package capability leaks, callback execution under realm authority, unsafe realm-context APIs, interface/canonical-type assumptions, rendering/security issues, deterministic execution concerns, and gas-risk patterns involving account coin enumeration.

Rules will only be promoted when they have a documented security rationale and reproducible vulnerable/fixed examples.

#### 2. Automated package publication monitoring

Integrate Sentinel with existing Gno.land indexing infrastructure rather than building another blockchain indexer.

Deliverables:

- tx-indexer integration;
- detection of newly published packages such as `MsgAddPackage`;
- automatic package/source resolution;
- automatic Sentinel analysis after package publication;
- package and finding history storage;
- deduplication of previously analyzed package versions;
- network and block metadata associated with findings.

The intended flow is:

```text
Gno.land
    ↓
tx-indexer
    ↓
Gno Sentinel ingestion
    ↓
package/source resolver
    ↓
security analyzer
    ↓
finding history
```

#### 3. Developer security API and notifications

Provide machine-readable access to Sentinel results.

Deliverables:

- API for retrieving findings for a realm/package;
- package security history;
- JSON finding schema;
- webhook notifications for new findings;
- CI examples;
- integration documentation;
- examples for explorers, developer tools, and monitoring services.

Representative API endpoints could include:

```text
GET /realm/{path}/findings
GET /realm/{path}/history
GET /rules
```

#### 4. Security monitoring interface

Build a lightweight interface for viewing continuously analyzed packages.

Deliverables:

- newly published package feed;
- findings grouped by severity and rule;
- package security history;
- links to source and transaction context;
- rule descriptions and remediation information;
- filtering by network, rule, severity, and package.

This interface is intended as a developer/security tool rather than a replacement for existing Gno explorers.

#### 5. Open-source rule framework

Make Sentinel extensible for Gno contributors and security researchers.

Each production rule should contain:

- stable rule ID;
- title and severity;
- confidence;
- affected Gno-version range where known;
- technical description;
- security invariant;
- vulnerable fixture;
- fixed fixture;
- unit/regression tests;
- remediation guidance;
- references to relevant Gno documentation or implementation behavior.

The goal is for new security knowledge discovered in audits, fuzzing, GnoVM development, or incident analysis to be converted into reusable ecosystem-wide detection.

### Impact on gno.land’s developer ecosystem

Gno Sentinel would provide security infrastructure at several points in the Gno development lifecycle.

Before deployment, developers could run:

```sh
gno-sentinel scan ./my-realm
```

or integrate the scanner into CI to detect known risky patterns before publishing a package.

After deployment, newly published packages could be detected automatically and analyzed without requiring each developer to manually opt into a security service.

For security researchers, Sentinel would provide a structured framework for converting Gno-specific vulnerability patterns into reproducible rules and regression tests.

For ecosystem tooling, explorers, wallets, monitoring services, and developer platforms could consume Sentinel's JSON/API output instead of independently implementing security analysis.

The project intentionally reuses existing Gno infrastructure, particularly tx-indexer, instead of duplicating indexing or explorer functionality.

The broader goal is to create a feedback loop:

```text
security research
      ↓
validated Gno security pattern
      ↓
Sentinel rule + regression fixtures
      ↓
CI / package scanning / monitoring
      ↓
earlier detection across the ecosystem
```

### Timeline and milestones

Proposed initial grant duration: **12 weeks**.

#### Milestone 1 — Analyzer foundation and rule framework
**Weeks 1–4**

Deliverables:

- stabilize package-aware analyzer architecture;
- integrate additional Gno-native parsing/semantic information where feasible;
- formalize the rule metadata/versioning system;
- expand to approximately 8–10 validated security rules;
- vulnerable/fixed fixtures for every production rule;
- deterministic regression tests;
- CI examples and documentation;
- documented rule-development process.

Acceptance criteria:

- `gno-sentinel scan <package>` operates deterministically;
- text and JSON output are stable;
- every production rule has reproducible vulnerable/fixed tests;
- the full test suite runs automatically in CI.

#### Milestone 2 — Chain ingestion and automatic scanning
**Weeks 5–8**

Deliverables:

- tx-indexer integration;
- new package-publication detection;
- package/source retrieval;
- automatic analysis pipeline;
- persistent package/finding history;
- block, transaction, network, and package metadata;
- retry/error handling;
- local integration tests and a public testnet demonstration.

Acceptance criteria:

```text
package published
→ detected
→ source retrieved
→ Sentinel analysis executed
→ findings stored
→ structured result available
```

without requiring manual triggering.

#### Milestone 3 — Developer-facing security service
**Weeks 9–12**

Deliverables:

- findings API;
- webhook notifications;
- CI/integration documentation;
- lightweight security dashboard;
- package history views;
- public testnet deployment/demo;
- installation and operating documentation;
- contributor documentation for implementing additional rules;
- final milestone report and public demonstration.

Acceptance criteria:

A developer can:

1. scan a local Gno project;
2. receive structured findings in CI;
3. inspect automatically analyzed published packages;
4. retrieve findings programmatically;
5. subscribe to security notifications.

## Contributions or related work for gno.land (if applicable)

Development of Gno Sentinel has already started independently before requesting grant funding.

Current project:

https://github.com/SuperSmile0426/gno-sentinel

The current prototype includes a Go CLI, package-aware `.gno` source loading, AST-based security analysis, cross-file analysis support, structured findings, human-readable and JSON reports, source evidence extraction, Gno-version/network analysis metadata, regression fixtures, automated GitHub Actions testing, and four initial Gno-specific rules.

The v0.2 analyzer work is available here:

https://github.com/SuperSmile0426/gno-sentinel/pull/1

This prototype was built after studying Gno's security documentation, runtime/realm security semantics, existing audit-pattern tooling, and tx-indexer architecture.

One important design decision is that Sentinel does not simply wrap existing textual audit checks. The project is moving toward package-aware and semantic analysis so that security rules can eventually reason about behavior across files and reduce avoidable false positives and false negatives.

## Why are you and your team well-suited for this project?

I am a software engineer and security researcher focused on practical vulnerability research and building reproducible security tooling.

My approach emphasizes source-level analysis, explicit security invariants, reproducible vulnerable and fixed cases, deterministic proof and evidence, distinguishing demonstrated vulnerabilities from heuristic warnings, and turning discovered vulnerability classes into reusable audit checks.

Gno Sentinel is already beyond the idea stage. A working prototype, rule engine, tests, fixtures, CI pipeline, package model, reporting layer, and architecture for future chain ingestion already exist.

This reduces execution risk for the grant: the proposed work extends an existing implementation rather than beginning with a speculative design.

I intend to develop the project openly, keep the analyzer and rule definitions available to the Gno ecosystem, and document the reasoning and limitations behind each detection rule.

## Referrals or examples of past work

Primary project and current implementation:

https://github.com/SuperSmile0426/gno-sentinel

GitHub profile:

https://github.com/SuperSmile0426

Relevant work includes independent smart-contract and blockchain security research, source-code auditing, reproducible vulnerability analysis, and development of security analysis tooling.

## Proposed funding

**Requested funding: USD 48,000 equivalent, milestone-based**

| Milestone | Duration | Funding |
|---|---:|---:|
| Analyzer foundation and expanded security rules | Weeks 1–4 | $14,000 |
| tx-indexer ingestion and automated package analysis | Weeks 5–8 | $16,000 |
| API, notifications, dashboard and public deployment | Weeks 9–12 | $18,000 |
| **Total** | **12 weeks** | **$48,000** |

The funding amount and milestone structure are open to adjustment based on feedback from the Gno engineering and grants teams.

The requested funding is for future development and delivery of the milestones described above; the existing prototype was developed before the grant application.
