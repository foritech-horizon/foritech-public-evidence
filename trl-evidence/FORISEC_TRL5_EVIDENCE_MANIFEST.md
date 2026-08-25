# FORISEC TRL 5 Evidence Manifest

## 1. Document control

| Field | Value |
|---|---|
| Document title | FORISEC TRL 5 Evidence Manifest |
| Status | **EVIDENCE INVENTORIED — DEMONSTRATION AND ACCEPTANCE PENDING** |
| Project | FORISEC |
| Call | HORIZON-CL3-2026-02-CS-ECCC-01 |
| Evidence owner | Foritech EOOD |
| Preparation date/time (UTC) | 2026-08-25T06:57:37Z |
| Manifest version | 1.0 |
| Scope statement | This manifest inventories real, currently-existing evidence supporting the minimal integrated FORISEC starting TRL 5 provenance-to-verification chain. It does not itself render a TRL acceptance decision — that decision is reserved for the separate `FORISEC_TRL5_ACCEPTANCE_RECORD.md` (not created by this task). |

## 2. TRL 5 scope boundary

This manifest addresses only the minimal pre-project provenance-to-verification chain:

1. Evidence generation / input preparation
2. Authenticated evidence binding
3. ML-DSA-65 signing and FTECH packaging
4. Submission through the declared client/service boundary
5. Verification by the authoritative Verification Core
6. Structured `status`, `reason`, and authenticated-claims result
7. Auditable evidence of the decision

**This manifest does NOT prove final FORISEC integrated TRL 7, and the following are explicitly excluded as M1 evidence:**

- Future FPGA implementation
- WP3 embedded Results (RTU)
- SOLARIX operational pilot
- Future formal model
- Future certification or regulatory conformity
- Multi-site / fleet orchestration
- Automatic vulnerability patching

## 3. Repository anchor table

| Repository | Role in chain | Branch | Commit | Relevant tag | Clean state | Observed UTC |
|---|---|---|---|---|---|---|
| `foritech-secure-system` (`/home/forybg/code/foritech-secure-system`, `git@github.com:foritech-secure-system/foritech-secure-system.git`) | Verification Core, crypto layer, private test/fuzz/benchmark suites, ADRs, closure records | main | `2291f053c5beb69d170291782c5e51cdcd99764e` (HEAD; audited baseline evidence below is anchored separately at historical commit `0844dbd`, see §4/§8) | — | Clean (`## main...origin/main`, no divergence) | 2026-08-25T06:57:37Z |
| `forisec-integration-pack` (`/home/forybg/code/forisec-integration-pack`, `git@github.com:foritech-horizon/forisec-integration-pack.git`) | Public verification contract, JSON schemas, fixtures, expected-results answer key | main | `dba998ab3b2fe43a907d4885da857a80efd72786` | `v1.0.0` → commit `dba998ab3b2fe43a907d4885da857a80efd72786` (tag object SHA `a81f05494736c9e8861abb3829b2bfa4a55d2ae7`; tag matches HEAD) | Clean | 2026-08-25T06:57:37Z |
| `foritech-public-evidence` (`/home/forybg/code/foritech-horizon/foritech-public-evidence`, `git@github.com:foritech-horizon/foritech-public-evidence.git`) | Public-facing test/benchmark/fuzzing summaries, KPI/TRL matrix | main | `5891492775aacb6e2568c8e759a108d7f9161856` | — | Clean | 2026-08-25T06:57:37Z |
| `foritech-cl3-2026` (`/home/forybg/code/foritech-horizon/foritech-cl3-2026`) | Canonical baseline/audit records (M1 baseline, WP2 rewrite draft) referenced for cross-repo test-count reconciliation | main | `e6c52cbaab2cf8987a888ee4d83129551d7256e3` | — | Working tree clean; local branch is **1 commit ahead of `origin/main`** (unpushed commit `e6c52cb`, unrelated to this manifest: LoI sync note) | 2026-08-25T06:57:37Z |

## 4. Evidence inventory

| Evidence ID | Repository | Exact path | Evidence class | Produced/updated | Relevant commit/tag | Supports chain step | Verification status | Limitation |
|---|---|---|---|---|---|---|---|---|
| E-CORE-001 | forisec-integration-pack | `CONTRACT.md` | Verification contract specification | Tag `v1.0.0` release | `dba998a` / `v1.0.0` | Steps 3, 5, 6 | Independently re-verified per `expected-results/README.md` | Documents contract family 1.0 / internal facade 0.1 only |
| E-CORE-002 | forisec-integration-pack | `REASON_CODES.md` | Reason-code taxonomy | Tag `v1.0.0` release | `dba998a` / `v1.0.0` | Step 6 | As above | — |
| E-CORE-003 | forisec-integration-pack | `schemas/verification-result.schema.json` | JSON schema | Tag `v1.0.0` release | `dba998a` / `v1.0.0` | Step 6 | Schema-validated per expected-results README | — |
| E-CORE-004 | forisec-integration-pack | `schemas/authenticated-claims.schema.json` | JSON schema | Tag `v1.0.0` release | `dba998a` / `v1.0.0` | Steps 2, 6 | Schema-validated per expected-results README | — |
| E-FIXTURE-001..008 | forisec-integration-pack | `fixtures/*.ftech`, `fixtures/*.bin` (8 files: `garbage`, `invalid_signature`, `missing_nonce`, `stale_timestamp`, `tampered_header`, `tampered_payload`, `valid_container`, `wrong_key`, plus 2 public-key `.bin` files) | Signed test containers / keys | Tag `v1.0.0` release | `dba998a` / `v1.0.0` | Steps 3, 4, 5 | Verified against `foritech.verification.verify_container()` and schema-validated, zero errors, per `CHANGELOG.md` 1.0.0 entry | `wrong_key.ftech` is intentionally byte-identical to `valid_container.ftech`. The negative condition is created solely by verification against `wrong_key_public.bin`, as defined in `fixtures/README.md` and `expected-results/expected_claims.json`. The identical container digest isolates wrong-key handling from container mutation and is expected test design, not an evidence discrepancy. |
| E-CONTRACT-001 | forisec-integration-pack | `expected-results/expected_claims.json` | Answer-key / expected verification verdicts | Tag `v1.0.0` release | `dba998a` / `v1.0.0` | Steps 5, 6, 7 | Independently re-verified, see `expected-results/README.md` | Verdict/claim values stable; raw digests/signature bytes are not (ML-DSA-65 non-determinism), per same README |
| E-ADR-001 | foritech-secure-system | `docs/decisions/0004-verification-contract-v0.1-freeze.md` | Architecture Decision Record | Historical (freeze decision underlying integration-pack contract family 1.0) | secure-system main | Step 3, 5 | Documentary | Not re-executed as code; policy record only |
| E-CLOSURE-001 | foritech-secure-system | `docs/status/MANAGED_VERIFICATION_BOUNDARY_V1_CLOSURE.md` | Closure/acceptance record | Audit date 2026-07-17, anchored at commit `0844dbd` | `0844dbd` (historical, not current HEAD) | Steps 4, 5, 6, 7 | Contains both automated pytest results and a manually-directed clean-room acceptance validation (explicitly **not** captured as pytest) — see §5 and §8 | States "Strong TRL 6" for the Managed Verification Boundary v1 deliverable specifically — this manifest does not adopt that statement as an integrated-FORISEC TRL claim (see §2) |
| E-BENCH-001 | foritech-secure-system | `docs/BENCHMARK_RESULTS.md` | Local benchmark report | Repository-current | secure-system main | Step 3, 5 (performance characterization, not functional chain proof) | Documentary — not re-run in this manifest task | Reports "AFL++ fuzz crashes: 0" — see §8 for the superseding fuzz methodology |
| E-BENCH-002 | foritech-public-evidence | `benchmarks/BENCHMARK_RESULTS.md` | Public benchmark report | Repository-current | public-evidence main | Step 3, 5 | Documentary — not re-run | Environment: Intel i7-5820K, Ubuntu 24.04.4, liboqs 0.14.0; also carries "Industrial Validation" PASS rows (ARM64 gateway, Debian 12, MQTT, replay protection, edge-to-cloud) — see §7 for record-only caveat |
| E-TEST-001 | foritech-public-evidence | `test-results/TEST_SUMMARY.md` | Public test summary | Repository-current | public-evidence main | Steps 4–7 | Documentary — superseded in scope, see §8 | Reports "115 | Passing: 115 | Failing: 0"; older/narrower summary than the M1 baseline's 504/177 figures |
| E-FUZZ-001 | foritech-public-evidence | `fuzzing/ATHERIS_FUZZING_2026-07-19.md` | Coverage-guided fuzzing campaign report | 2026-07-19 | public-evidence main (references secure-system fix commits `04bdaed`, `065ea55`, `2291f05`) | Step 5 (parser/stream/verify robustness) | Documentary — describes ~5.36 billion executions across 3 targets, 0 crashes, real edge/branch coverage confirmed growing | Explicitly supersedes the AFL++ "0 crashes" claim (that prior run had `map_size: 0.00%`, `edges_found: 0` — no real coverage signal); harness lives in private `foritech-secure-system` repo, not in this repo |
| E-KPI-001 | foritech-public-evidence | `kpi/KPI_TRL_MATRIX.md` | KPI/TRL summary | Repository-current | public-evidence main | N/A — claims document, not chain evidence | Documentary | Contains the unqualified line "TRL 7 Status: CONFIRMED — all KPIs met or exceeded" — **not accepted by this manifest**; see §8 |
| E-README-001 | foritech-public-evidence | `README.md` | Repository purpose statement | Repository-current | public-evidence main | N/A | Documentary | States "Public technical evidence supporting TRL 7 deployment claims" — **not accepted by this manifest**; see §8 |
| E-BASELINE-001 | foritech-cl3-2026 | `docs/baselines/FORISEC_M1_TECHNICAL_BASELINE.md` | Audited technical baseline record | Audit date 2026-07-17 | Anchors to secure-system commit `0844dbd`; cl3-2026 main | Steps 4–7 (reconciles E-CLOSURE-001 test counts) | Documentary — audit verdict recorded as `BASELINE_CONFIRMED_WITH_GAPS` | States the two test-count figures (504/177) are reported separately and are explicitly **not additive**; see §5, §8 |
| E-BASELINE-002 | foritech-cl3-2026 | `docs/baselines/FORISEC_WP2_REWRITE_DRAFT_ETAP3.md` | WP2 rewrite draft (references baseline test-count clarification) | Repository-current | cl3-2026 main | Cross-reference only | Documentary | Repeats the 504/177 non-additive clarification; not independent evidence |

## 5. Test and validation scope

| Test record | Repository/commit | Exact command or workflow | Environment | Passed | Failed | Skipped | Execution timestamp | Evidence path | Classification |
|---|---|---|---|---:|---:|---:|---|---|---|
| Startup smoke tests | foritech-secure-system @ `0844dbd` | `tests/managed_verification_e2e/test_server_startup.py` (pytest) | Not independently re-run in this task | 3 | 0 | 0 | 2026-07-17 (per closure doc) | `docs/status/MANAGED_VERIFICATION_BOUNDARY_V1_CLOSURE.md` §7 | DOCUMENTARY EVIDENCE — NOT RE-RUN |
| Authenticated managed E2E tests | foritech-secure-system @ `0844dbd` | `tests/managed_verification_e2e/` (pytest) | Not independently re-run | 11 | 0 | 0 | 2026-07-17 | Same as above | DOCUMENTARY EVIDENCE — NOT RE-RUN |
| Full repository suite (M1 baseline) | foritech-secure-system @ `0844dbd` | Full `pytest` suite (exact invocation not separately documented beyond "full repository suite") | Not independently re-run | 504 | 0 | 4 | 2026-07-17 | `docs/status/MANAGED_VERIFICATION_BOUNDARY_V1_CLOSURE.md` §7; corroborated in `foritech-cl3-2026/docs/baselines/FORISEC_M1_TECHNICAL_BASELINE.md` | DOCUMENTARY EVIDENCE — NOT RE-RUN |
| Managed Verification + authenticated E2E (M1 baseline, focused run) | foritech-secure-system @ `0844dbd` | Focused pytest execution of Managed Verification / E2E tests, incl. live Uvicorn server flow | Not independently re-run | 177 | 0 | 0 | 2026-07-17 | `foritech-cl3-2026/docs/baselines/FORISEC_M1_TECHNICAL_BASELINE.md` | DOCUMENTARY EVIDENCE — NOT RE-RUN. Reported **separately** from, and not additive with, the 504/4 full-suite figure (explicit clarification in source doc) |
| Clean-room Step 9D acceptance validation | foritech-secure-system @ `0844dbd` | Manually directed sequence: fresh git worktree + fresh venv + native liboqs build at pinned commit; container generation, tampered container, real HTTP calls to managed endpoint, 3 revoke scenarios, restart, graceful stop, backup/recovery dry run | Fresh, isolated environment per source doc | N/A (not an automated suite) | N/A | N/A | 2026-07-17 (implied, same closure cycle) | `docs/status/MANAGED_VERIFICATION_BOUNDARY_V1_CLOSURE.md` §7 | DOCUMENTARY EVIDENCE — NOT RE-RUN. Source document explicitly states this was **not** captured as a pytest suite |
| Public test summary (older, superseded scope) | foritech-public-evidence | Not documented beyond aggregate result | Not documented | 115 | 0 | 0 | Undated in source (repository-current) | `test-results/TEST_SUMMARY.md` | DOCUMENTARY EVIDENCE — NOT RE-RUN; narrower/older scope than the M1 baseline, see §8 |
| Atheris coverage-guided fuzzing (parser/stream/verify) | foritech-secure-system (harness in `tools/fuzz_atheris/`), fixes at commits `04bdaed`, `065ea55`, `2291f05` | `tools/fuzz_atheris/run_all.sh [seconds_per_target]` (default 14400s/target, launches 3 tmux sessions: `fuzz-parser`, `fuzz-stream`, `fuzz-verify`) | Instrumented Python bytecode via Atheris (libFuzzer-based); ~4h/target | 0 crashes across ~5.36B total execs | — | — | 2026-07-19 | `foritech-public-evidence/fuzzing/ATHERIS_FUZZING_2026-07-19.md` | DOCUMENTARY EVIDENCE — NOT RE-RUN. "Passed/failed/skipped" is not the applicable metric; recorded as crash count and edge/coverage growth instead |
| Full test suite after Atheris-driven fixes | foritech-secure-system, fixes applied before this run | Full pytest suite (post-fix) | Not documented beyond aggregate result | 523 | 0 | 4 | 2026-07-19 (same campaign doc) | `foritech-public-evidence/fuzzing/ATHERIS_FUZZING_2026-07-19.md` | DOCUMENTARY EVIDENCE — NOT RE-RUN. This is a **later, post-fix** full-suite count than the 504/4 M1 baseline figure (`0844dbd`, 2026-07-17) — the two are not the same measurement and must not be conflated; see §8 |
| Historical AFL++ blackbox fuzzing | foritech-secure-system | AFL++, non-instrumented (`-n` flag) | Blackbox, 3–6 seed files | 0 crashes reported | — | — | Predates 2026-07-19 (undated precisely in source) | `docs/BENCHMARK_RESULTS.md`; `foritech-public-evidence/benchmarks/BENCHMARK_RESULTS.md` | DOCUMENTARY EVIDENCE — NOT RE-RUN. Superseded methodology — re-inspection of raw `plot_data` showed `map_size: 0.00%` / `edges_found: 0` for the full run duration, i.e. no real coverage signal; "0 crashes" under those conditions is materially weaker than it reads, per `ATHERIS_FUZZING_2026-07-19.md` |
| Schema/fixture validation (integration pack) | forisec-integration-pack @ `v1.0.0` (`dba998a`) | Validation against `verify_container()` + both JSON schemas (exact script not present in the integration-pack repo itself — described narratively in `expected-results/README.md` and `CHANGELOG.md`) | Not independently re-run | "zero errors" (no numeric pass count given) | 0 | — | Tag `v1.0.0` release | `forisec-integration-pack/expected-results/README.md`, `CHANGELOG.md` | DOCUMENTARY EVIDENCE — NOT RE-RUN |

## 6. Integration-pack evidence

**`v1.0.0` demonstrates:**
- The verification contract (`CONTRACT.md`)
- The three-status model (documented in the contract)
- The reason-code taxonomy (`REASON_CODES.md`)
- Authenticated claims (schema + expected-results answer key)
- Two JSON schemas (`verification-result.schema.json`, `authenticated-claims.schema.json`)
- Eight fixtures (`garbage`, `invalid_signature`, `missing_nonce`, `stale_timestamp`, `tampered_header`, `tampered_payload`, `valid_container`, `wrong_key` — plus two public-key `.bin` files)
- Expected results (`expected_claims.json`)
- Validation against the frozen internal facade (contract family 1.0 ↔ internal facade `FACADE_VERSION = "0.1"`, frozen under ADR-0004 in `foritech-secure-system`)

**`v1.0.0` does NOT demonstrate:**
- Complete relevant-environment deployment
- Managed Verification authentication/deployment by itself
- An operational pilot
- Final FORISEC TRL 7

## 7. Environment and platform records

| Record | Source | Detail | Status |
|---|---|---|---|
| OS / Kernel | `foritech-secure-system/docs/BENCHMARK_RESULTS.md` | Ubuntu 24.04.4 LTS, kernel 6.17.0-29-generic | Documentary |
| CPU/platform | Same | Intel i7-5820K @ 3.30GHz | Documentary |
| Library versions | Same | liboqs 0.14.0; ML-DSA-65 (FIPS 204) + ML-KEM-768 (FIPS 203) | Documentary |
| GitHub Actions environment | Same | ubuntu-24.04, 2 vCPU; 4,075 verifications/sec throughput reported | Documentary |
| Industrial ARM64 gateway | `foritech-public-evidence/benchmarks/BENCHMARK_RESULTS.md` | Listed as "PASS" in an "Industrial Validation" table | **Single-line table entry only** — no separately verifiable supporting record (raw logs, dated run, or environment spec) was located in the repositories checked for this manifest; not elevated beyond what the source states |
| Debian 12 / MQTT transport | Same | Listed as "PASS" | Same caveat as above — table entry only, no separate supporting record found |
| Replay protection / edge-to-cloud | Same | Listed as "PASS" | Same caveat |
| Test date | `docs/status/MANAGED_VERIFICATION_BOUNDARY_V1_CLOSURE.md`, `FORISEC_M1_TECHNICAL_BASELINE.md` | Audit date 2026-07-17, baseline commit `0844dbd` | Documentary |

## 8. Evidence discrepancy register

| Item | Classification | Notes |
|---|---|---|
| `115 passed` (`foritech-public-evidence/test-results/TEST_SUMMARY.md`) | **historical / different test scope** | Undated, category-based breakdown (tamper detection, replay, malformed containers, downgrade, performance, MQTT, edge-to-cloud, concurrent stress). Narrower and structured differently than the M1 baseline's full-suite figure. Not reconciled against a specific commit in the source document itself. |
| `523 passed, 4 skipped` (Atheris record, `foritech-public-evidence/fuzzing/ATHERIS_FUZZING_2026-07-19.md`) | **current, but distinct measurement** | This is the **full repository suite after** the three fuzzing-driven fixes (`04bdaed`, `065ea55`, `2291f05`) were applied and committed, dated 2026-07-19 — i.e., **later** than and **not the same run** as the 504/4 M1 baseline figure. Both are legitimate, but for different commits/dates. |
| `504 passed, 4 skipped` (audited proposal baseline, `0844dbd`, 2026-07-17) | **current — this is the M1-anchored baseline figure** | Cross-confirmed in both `foritech-secure-system/docs/status/MANAGED_VERIFICATION_BOUNDARY_V1_CLOSURE.md` and `foritech-cl3-2026/docs/baselines/FORISEC_M1_TECHNICAL_BASELINE.md`. Predates the Atheris campaign fixes by two days, so is **not** the same test population as the 523-passed figure above. |
| `177 passed` (Managed Verification / E2E evidence) | **current, reported separately** | From `FORISEC_M1_TECHNICAL_BASELINE.md`, same audit (`0844dbd`, 2026-07-17). Source document explicitly states this figure is reported **separately** from and **not additive** with the 504/4 full-suite figure — both are subsets/overlaps of the same test tree, not independent totals. |
| Old AFL++ `0 crashes` claim vs. superseding Atheris methodology | **superseded** | `ATHERIS_FUZZING_2026-07-19.md` explicitly states the AFL++ run was blackbox/non-instrumented (`-n` flag, 3–6 seeds) and that re-inspection of `plot_data` showed `map_size: 0.00%` and `edges_found: 0` for the entire run — no real coverage signal. Left in place in the repo as historical record, not deleted, but superseded as current methodology. |
| `README.md` statement "supporting TRL 7 deployment claims" (`foritech-public-evidence`) | **unresolved / not accepted by this manifest** | This manifest's scope (§2) is TRL 5, not TRL 7. The statement is reproduced here for completeness of the discrepancy register only; it is not treated as evidence toward the TRL 5 chain, and its accuracy as a TRL 7 claim is outside this manifest's scope to adjudicate. |
| `kpi/KPI_TRL_MATRIX.md` statement "TRL 7 Status: CONFIRMED — all KPIs met or exceeded" (`foritech-public-evidence`) | **unresolved / not accepted by this manifest** | Same treatment as above — an unqualified TRL 7 claim is not adopted, cross-checked, or endorsed by this TRL 5 evidence manifest. |

**`TRL 7 confirmed` is not accepted anywhere in this manifest as integrated FORISEC maturity evidence.** Where source documents make TRL 6 or TRL 7 statements (e.g., the closure record's "Strong TRL 6" statement, scoped explicitly to the Managed Verification Boundary v1 deliverable; or the public-evidence README/KPI matrix TRL 7 statements), this manifest reproduces them for traceability only and does not extend, generalize, or adopt them as FORISEC-integrated TRL evidence.

## 9. Integrity and reproducibility

SHA-256 digests computed directly from the repository working trees during preparation of this manifest (2026-08-25T06:57:37Z UTC). Reproduction of the underlying test/fuzz/benchmark **results** themselves was **not** performed in this task (see §5 — all entries classified `DOCUMENTARY EVIDENCE — NOT RE-RUN`); only digesting of the static evidence documents/fixtures was carried out.

| Evidence file | Repository @ commit | SHA-256 |
|---|---|---|
| `CONTRACT.md` | forisec-integration-pack @ `dba998a` | `da19ea2655d69733e5a751a7429bdf87c13f5215b6f1bb8bebc404e1b4869070` |
| `REASON_CODES.md` | forisec-integration-pack @ `dba998a` | `9e27897a2f1ae1ae6d9d48ffba6d17060218d257aef4f04636bb81a2e990645e` |
| `schemas/authenticated-claims.schema.json` | forisec-integration-pack @ `dba998a` | `3af996bcf2c76d337dd2c45c8ae2b8ace2c851070f4e543ebc2ed9e8ead2cb2d` |
| `schemas/verification-result.schema.json` | forisec-integration-pack @ `dba998a` | `b24f3bf4a9ddaa89f32784f65e9736f5f24ed577a740a15f0754e41d5e3513ef` |
| `expected-results/expected_claims.json` | forisec-integration-pack @ `dba998a` | `2ed3ceccd1eb81f462278679eb4b4dead2c6b7b1ec0ba564bdd3eababd1fd2da` |
| `fixtures/garbage.ftech` | forisec-integration-pack @ `dba998a` | `0fd84a3e9fd93090b2064368635e3caa8866abfbc2c3c79b9e4fd3f2c03e0067` |
| `fixtures/invalid_signature.ftech` | forisec-integration-pack @ `dba998a` | `a48587fccc1d2836db82e1554998328b3c672d4edcb6d540c8d9e32926e21cc2` |
| `fixtures/missing_nonce.ftech` | forisec-integration-pack @ `dba998a` | `250824ffb556f68e52f4896d91892798c3868e9f7ca2b14e460aa2e1971da77f` |
| `fixtures/stale_timestamp.ftech` | forisec-integration-pack @ `dba998a` | `bbc47edd7974b63a4b6c1c5acf67c74836c5e63a8779df9110dfc3f73cd5821e` |
| `fixtures/tampered_header.ftech` | forisec-integration-pack @ `dba998a` | `1e4b548d3059fe03f8ee3351d3a7e234855308891518e6fef97ee219a751e753` |
| `fixtures/tampered_payload.ftech` | forisec-integration-pack @ `dba998a` | `d06673ab8d79d1a6e1b9df624835529f757709f0dc4d6727b6353aa37ffea055` |
| `fixtures/valid_container.ftech` | forisec-integration-pack @ `dba998a` | `d4460f6ce34274b148da27084e5b78783e661a5bc339caac5bd817cd71a4492d` |
| `fixtures/wrong_key.ftech` | forisec-integration-pack @ `dba998a` | `d4460f6ce34274b148da27084e5b78783e661a5bc339caac5bd817cd71a4492d` (intentionally identical to `valid_container.ftech`; verified with `wrong_key_public.bin` to isolate the wrong-key rejection condition) |
| `fixtures/valid_public_key.bin` | forisec-integration-pack @ `dba998a` | `d1b301dcf2e45238ea6a22f0ba8a149ddcc6247d9ec1a0fb8e7d7956cf493ad5` |
| `fixtures/wrong_key_public.bin` | forisec-integration-pack @ `dba998a` | `db7d6c9ed8be7f189db9b09f3657f9891d59e0dd25454ad0f10d0f8f8b1f7a30` |
| `VERSION` | forisec-integration-pack @ `dba998a` | `59854984853104df5c353e2f681a15fc7924742f9a2e468c29af248dce45ce03` |
| `docs/status/MANAGED_VERIFICATION_BOUNDARY_V1_CLOSURE.md` | foritech-secure-system @ `2291f05` (doc content anchored to audit at `0844dbd`) | `2b5f35aa40ddfeb8be88e8dc48ac7bd47ba8fee3835d668cbb4d50fa6daad033` |
| `docs/decisions/0004-verification-contract-v0.1-freeze.md` | foritech-secure-system @ `2291f05` | `77fe861ffc3127b7af8631e3c4db0dac67198f0919f56d669bf5bb14c2309f85` |
| `docs/BENCHMARK_RESULTS.md` | foritech-secure-system @ `2291f05` | `a29dd4c400c5d5ae4f6d22d790e70fe7e9cc6dd488ad43aa44700a81ebf223b6` |
| `README.md` | foritech-public-evidence @ `5891492` | `6e00980124c355b93e125e6ee5a5a2a90b8fc9f91eecb63d630eab0a4d0db1c7` |
| `test-results/TEST_SUMMARY.md` | foritech-public-evidence @ `5891492` | `96b9bde0c377fd4659b015923699a12f85df9053169586e4783eb0a794f1e500` |
| `benchmarks/BENCHMARK_RESULTS.md` | foritech-public-evidence @ `5891492` | `3650b13b7cbef23e92d4e276faa8a5b8929327a0460e704e9d33393614c61c9c` |
| `fuzzing/ATHERIS_FUZZING_2026-07-19.md` | foritech-public-evidence @ `5891492` | `a1f986e11714f22aed69ae5bac1413ef6cd8822e416db87486548fff1adc232b` |
| `kpi/KPI_TRL_MATRIX.md` | foritech-public-evidence @ `5891492` | `a5fde5ccd5828041dab772f8b36104a8fb0ce9b58082fb143fd90fa588f4c753` |
| `docs/baselines/FORISEC_M1_TECHNICAL_BASELINE.md` | foritech-cl3-2026 @ `e6c52cb` | `aa26b1b1361730d48559311d58cb115135e422ce1ada07b5fc160bb263c31300` |
| `docs/baselines/FORISEC_WP2_REWRITE_DRAFT_ETAP3.md` | foritech-cl3-2026 @ `e6c52cb` | `738de41ae82f3f8abd6b480c9f2c3ae4947028d7528e33b878a796da47f75456` |

No secrets, private keys, credentials, tokens, personal data, or proprietary source content are reproduced in this manifest.

## 10. Manifest conclusion

**EVIDENCE INVENTORIED — RELEVANT-ENVIRONMENT DEMONSTRATION AND FORMAL TRL 5 ACCEPTANCE REMAIN PENDING**
