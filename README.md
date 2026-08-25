# Foritech Public Evidence Repository

**Project Concept:** FORISEC
**Target Call:** HORIZON-CL3-2026-02-CS-ECCC-01
**Coordinator:** Foritech (foritech.bg | forisec.eu)
**Purpose:** Public technical evidence supporting the Foritech Secure System's starting technical baseline (**Starting TRL 5**) and performance/security characteristics measured against the **Target TRL 7** thresholds FORISEC aims to reach through the project.

Performance metrics and test results are generated from automated CI benchmark and validation workflows. Raw benchmark artifacts and validation reports are available upon request.

## Contents

| Folder | Contents |
|--------|----------|
| `benchmarks/` | Performance measurements — ML-DSA-65 signing, verification throughput, payload latency |
| `test-results/` | CI test suite results — 115 pass, 0 fail |
| `kpi/` | KPI matrix — component-level measurements against TRL 7 target thresholds |
| `fuzzing/` | Coverage-guided fuzzing campaign (Atheris), supersedes earlier non-instrumented AFL++ results |

## Key Metrics (generated via GitHub Actions CI)

| Metric | Result |
|--------|--------|
| ML-DSA-65 signing | 0.1 ms avg |
| Full verification | 0.1 ms avg |
| Throughput | 4,075 verifications/sec |
| Container overhead | 7,772 bytes (constant) |
| Coverage-guided fuzz crashes (Atheris, ~5.36B execs) | 0 (after remediation — see `fuzzing/`) |
| Test coverage | 115 pass, 0 fail |

> Benchmark performed on commodity hardware (Intel i7-5820K, 2014).
> Production deployments on modern infrastructure are expected to achieve proportionally higher throughput.

## Validated On

- Ubuntu 24.04 LTS
- Industrial ARM64 gateway platform
- Debian 12

## TRL positioning

This repository documents component-level performance and security-testing evidence for the Foritech Secure System as it currently exists (**Starting TRL 5**). These measurements support FORISEC's proposal to extend and validate the system toward **integrated TRL 7** through embedded validation, hardware acceleration, formal security analysis and an operational pilot — TRL 7 is the project's target, not a status already reached. Meeting or exceeding an individual performance threshold below does not, by itself, constitute an integrated TRL 7 claim.

## What is NOT here

Core verification engine source code, internal API implementation, and deployment infrastructure are proprietary and maintained in a private repository.

---
*forisec.eu | foritech.bg*
