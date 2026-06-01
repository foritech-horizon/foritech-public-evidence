# Foritech Public Evidence Repository

**Project:** FORISEC — HORIZON-CL3-2026-02-CS-ECCC-01  
**Coordinator:** Foritech (foritech.bg | forisec.eu)  
**Purpose:** Public verification evidence for the Foritech Secure System (TRL 7)

This repository contains **publicly verifiable evidence** of the Foritech verification platform's performance, security validation, and compliance status.

## Contents

| Folder | Contents |
|--------|----------|
| `benchmarks/` | Performance measurements — ML-DSA-65 signing, verification throughput, payload latency |
| `test-results/` | CI test suite results — 115 pass, 0 fail |
| `compliance/` | NIS2, CRA, FIPS 203/204 compliance checklist |
| `kpi/` | KPI vs TRL matrix |
| `architecture/` | System architecture diagrams and FTECH container specification |
| `trl-evidence/` | TRL 7 evidence documentation |

## Key Metrics (verified via GitHub Actions CI)

| Metric | Result |
|--------|--------|
| ML-DSA-65 signing | 0.1 ms avg |
| Full verification | 0.1 ms avg |
| Throughput | 4,075 verifications/sec |
| Container overhead | 7,772 bytes (constant) |
| AFL++ fuzz crashes | 0 |
| Test coverage | 115 pass, 0 fail |

## Verified On

- Ubuntu 24.04 LTS
- Industrial ARM64 gateway platform
- Debian 12

## What is NOT here

Core verification engine source code, internal API implementation, and deployment infrastructure are proprietary and maintained in a private repository.

---
*forisec.eu | foritech.bg*
