# Gno Sentinel

- GitHub handle: **SuperSmile0426**
- Email: **superjodev426@gmail.com**
- Links:
  - Project repository: https://github.com/SuperSmile0426/gno-sentinel
  - Current v0.2 development PR: https://github.com/SuperSmile0426/gno-sentinel/pull/1
  - GitHub profile: https://github.com/SuperSmile0426

## Project summary

Gno Sentinel is an open-source security tool for Gno.land realms.

The first part of the project is a package-aware static analyzer for `.gno` source code. It checks for Gno-specific security mistakes and produces human-readable and JSON findings that can be used locally or in CI.

The next part is automatic monitoring of newly published packages. Sentinel will use existing Gno.land indexing infrastructure to detect package publication, retrieve source, run the analyzer, and keep a history of findings.

The Gno grants README lists real-time contract monitoring and auditing as an example project. Gno Sentinel is focused on that area.

A working prototype already exists. The current v0.2 implementation includes:

- package-aware AST analysis across multiple `.gno` files;
- CLI and JSON output;
- severity-based CI failure thresholds;
- vulnerable/fixed regression fixtures;
- source excerpts in findings;
- optional Gno version and network metadata;
- four initial Gno-specific rules;
- an ingestion interface for later tx-indexer integration.

Current rules:

- `GNO-PAY-001` — `OriginSend()` without a recognized `IsUserCall()` control-flow guard;
- `GNO-AUTH-001` — unsafe `OriginCaller()` authorization patterns;
- `GNO-STATE-001` — exported pointers or getters that expose mutable package-level pointer state, including cross-file cases;
- `GNO-REALM-001` — unsafe `PreviousRealm()` use in realm-aware functions.

Sentinel is not a replacement for manual review. The goal is to catch known Gno-specific patterns early and make those checks reusable across projects.

### Goals and deliverables

#### 1. Gno-native static analyzer

Improve the current analyzer and make the rule framework stable enough for regular developer use.

Deliverables:

- stable CLI and JSON formats;
- package-level analysis across multiple `.gno` files;
- more Gno parser/type information where useful;
- Gno-version metadata for rules;
- deterministic finding order;
- file/line evidence and source excerpts;
- CI-compatible exit behavior;
- vulnerable and fixed fixtures for every production rule;
- documentation for each rule.

The first production rule set will focus on Gno-specific issues such as:

- caller identity mistakes;
- payment validation and `OriginSend()`;
- mutable-state pointer exposure;
- `/p/` package capability leaks;
- callbacks executed while a realm holds authority;
- unsafe realm-context APIs;
- interface/type assumptions;
- rendering issues;
- determinism issues;
- gas-risk patterns.

A rule will only be included as a production rule when it has a clear security rationale and reproducible vulnerable/fixed examples.

#### 2. Automatic package monitoring

Connect Sentinel to existing Gno.land indexing infrastructure.

Deliverables:

- tx-indexer integration;
- detection of new package publications such as `MsgAddPackage`;
- package/source retrieval;
- automatic analysis after publication;
- package and finding history;
- deduplication of already analyzed package versions;
- block, transaction, network, and package metadata.

Planned flow:

```text
Gno.land
    ↓
tx-indexer
    ↓
Gno Sentinel
    ↓
source resolver
    ↓
security analyzer
    ↓
finding history
```

#### 3. API and notifications

Provide machine-readable access to results.

Deliverables:

- API for realm/package findings;
- package security history;
- JSON finding schema;
- webhook notifications;
- CI examples;
- integration documentation.

Example endpoints:

```text
GET /realm/{path}/findings
GET /realm/{path}/history
GET /rules
```

#### 4. Security dashboard

Build a small interface for inspecting analyzed packages.

Deliverables:

- feed of newly analyzed packages;
- findings by severity and rule;
- package history;
- links to source and transaction context;
- rule descriptions and remediation guidance;
- filters by network, rule, severity, and package.

This is intended to complement existing explorers, not replace them.

#### 5. Open-source rule framework

Make it straightforward for contributors to add new checks.

Each production rule will include:

- stable rule ID;
- title and severity;
- confidence;
- affected Gno version range where known;
- technical description;
- security invariant;
- vulnerable fixture;
- fixed fixture;
- tests;
- remediation guidance;
- references to relevant Gno documentation or code.

### Impact on gno.land’s developer ecosystem

Before deployment, developers will be able to run:

```sh
gno-sentinel scan ./my-realm
```

and use the same checks in CI.

After deployment, Sentinel can automatically analyze newly published packages and keep a history of findings.

For security researchers, the rule framework provides a way to turn a confirmed Gno-specific vulnerability pattern into a reusable test.

For other ecosystem tools, the JSON/API output can be consumed by explorers, wallets, monitoring services, or developer platforms.

The project will reuse tx-indexer and other existing Gno infrastructure instead of building a separate indexer.

### Timeline and milestones

Proposed duration: **12 weeks**.

#### Milestone 1 — Analyzer and rule framework
**Weeks 1–4**

Deliverables:

- stabilize the package-aware analyzer;
- add Gno-native semantic information where practical;
- finalize rule metadata/versioning;
- expand to about 8–10 validated rules;
- add vulnerable/fixed fixtures for each production rule;
- deterministic regression tests;
- CI examples and documentation;
- contributor documentation for adding rules.

Acceptance criteria:

- `gno-sentinel scan <package>` produces deterministic results;
- text and JSON formats are stable;
- every production rule has vulnerable/fixed tests;
- the full suite runs in CI.

#### Milestone 2 — Chain ingestion and automatic scanning
**Weeks 5–8**

Deliverables:

- tx-indexer integration;
- new package detection;
- package/source retrieval;
- automatic scanning;
- persisted package/finding history;
- block, transaction, network, and package metadata;
- retry/error handling;
- testnet demonstration.

Acceptance criteria:

```text
package published
→ detected
→ source retrieved
→ analyzed
→ findings stored
```

without manual triggering.

#### Milestone 3 — API, notifications, and dashboard
**Weeks 9–12**

Deliverables:

- findings API;
- webhook notifications;
- CI/integration documentation;
- lightweight dashboard;
- package history views;
- public testnet deployment/demo;
- installation and operating documentation;
- final milestone report.

Acceptance criteria:

A developer can:

1. scan a local Gno project;
2. use the scanner in CI;
3. inspect automatically analyzed packages;
4. retrieve findings through an API;
5. subscribe to notifications.

## Contributions or related work for gno.land (if applicable)

I started Gno Sentinel before applying for this grant.

Project repository:

https://github.com/SuperSmile0426/gno-sentinel

Current v0.2 work:

https://github.com/SuperSmile0426/gno-sentinel/pull/1

The current prototype includes:

- Go CLI;
- package-aware `.gno` source loading;
- AST-based rules;
- cross-file analysis;
- structured findings;
- text and JSON reports;
- source evidence;
- Gno version/network metadata;
- regression fixtures;
- GitHub Actions CI;
- four initial Gno-specific rules.

I built the prototype after reviewing Gno's security documentation, realm/runtime security model, existing audit-pattern tooling, and tx-indexer architecture.

I do not want Sentinel to be a UI around existing text-pattern checks. The current direction is package-aware analysis with better semantic context and version-aware rules.

## Why are you and your team well-suited for this project?

I am a software engineer and security researcher working on source-code auditing, vulnerability research, and security tooling.

My security work is centered on reproducible results: identify the security property, build a vulnerable case, build a fixed case, and keep the test as a regression check.

That fits this project well because each Sentinel rule needs to be tied to an actual Gno security property instead of relying on generic smart-contract assumptions.

The prototype is already working, so the grant would fund the next development steps rather than the initial idea.

I plan to keep the analyzer, rule definitions, fixtures, and documentation open source.

## Referrals or examples of past work

Gno Sentinel:

https://github.com/SuperSmile0426/gno-sentinel

GitHub profile:

https://github.com/SuperSmile0426

My related work includes smart-contract and blockchain security research, source-code auditing, reproducible vulnerability testing, and security tooling.

## Proposed funding

**Requested funding: USD 48,000 equivalent, milestone-based**

| Milestone | Duration | Funding |
|---|---:|---:|
| Analyzer and expanded security rules | Weeks 1–4 | $14,000 |
| tx-indexer integration and automatic package analysis | Weeks 5–8 | $16,000 |
| API, notifications, dashboard, and public deployment | Weeks 9–12 | $18,000 |
| **Total** | **12 weeks** | **$48,000** |

I am open to adjusting the scope, milestone split, or funding amount based on feedback from the Gno engineering and grants teams.

The requested funding is for the future work described above. The existing prototype was built before this application.
