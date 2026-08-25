# FORISEC TRL 5 Limitations and Gaps Record

## 1. Document control

| Field | Value |
|---|---|
| Title | FORISEC TRL 5 Limitations and Gaps Record |
| Project/Call | FORISEC / HORIZON-CL3-2026-02-CS-ECCC-01 |
| Evidence owner | Foritech EOOD |
| Preparation date (UTC) | 2026-08-25 |
| Document version | 1.0 |
| Status | **BASELINE LIMITATIONS AND GAPS RECORDED** |
| Referenced repository commits | `foritech-secure-system` @ `2291f053c5beb69d170291782c5e51cdcd99764e` (HEAD); `forisec-integration-pack` @ `dba998ab3b2fe43a907d4885da857a80efd72786` (tag `v1.0.0`); `foritech-public-evidence` @ `f49f4c7b498a43fdea5ec6ec9c3829de63888a34` (HEAD at preparation time); `foritech-cl3-2026` @ `e6c52cbaab2cf8987a888ee4d83129551d7256e3` (referenced read-only for WP scope only) |
| Relationship to the manifest and demonstration | This document does not add new technical evidence and does not re-run tests. It reads and cross-checks `FORISEC_TRL5_EVIDENCE_MANIFEST.md` (commit `aedd4450d5b79a03c96b547580dd9c97a9697224`) and `FORISEC_TRL5_RELEVANT_ENVIRONMENT_DEMONSTRATION.md` (commit `f49f4c7`) and records what those two documents do and do not establish, plus what remains outside their scope. Formal TRL 5 acceptance is not declared here — that belongs to a separate `FORISEC_TRL5_ACCEPTANCE_RECORD.md`, not created by this task. This record is not an AI-assistant approval of anything; it is a scope-control document for human review. |

## 2. Purpose and interpretation rule

This is a **scope-control and claim-boundary document**, not a new evidence-generation exercise. Its purpose is to state precisely what is and is not covered by the two completed TRL 5 evidence documents, so that the demonstrated baseline cannot be silently over-claimed or under-claimed in later proposal or reporting text.

Interpretation rules that apply throughout this document:

- A listed limitation does **not** invalidate the demonstrated bounded-Foritech TRL 5 baseline **unless it is explicitly marked as a critical acceptance blocker** in §5.
- Future project Results (WP3/RTU, WP4/Logiicdev, WP5/Solarix, and later WP2 work) must **not** be described as already implemented at M1/TRL 5.
- Absence of a capability from the M1 baseline does **not** automatically mean it is absent from the planned FORISEC project — most such items are explicitly planned future work, tracked in §7.
- This document does not redesign, reinterpret, or override the Work Plan, the M1 Technical Baseline, or the two completed TRL evidence documents. Where this record and any of those sources appear to disagree, that disagreement is reported in §5 or §8 rather than silently resolved.

## 3. Confirmed M1/TRL 5 evidence boundary

The following capabilities are genuinely supported by `FORISEC_TRL5_EVIDENCE_MANIFEST.md` and/or `FORISEC_TRL5_RELEVANT_ENVIRONMENT_DEMONSTRATION.md`, and no more than what those two documents state:

- **Verification Core and the canonical `verify_container()` facade** — exercised directly in the demonstration (DEMO-POS-001, DEMO-NEG-001..005, DEMO-CTX-001..002), backed by the manifest's E-CORE-001..004 and E-ADR-001 entries.
- **Synthetic FTECH container generation and parsing** — via the canonical `tools/generate_test_fixtures.py` generator, producing 8 fresh containers per run (demonstration §5 Step 1, §6.1).
- **ML-DSA-65 signing and verification** — exercised in container generation and Core verification (demonstration §5 Steps 2–3, §7).
- **Authenticated container claims and metadata binding** — DEMO-POS-001 confirms `device_id`, `kid`, `nonce`, `timestamp`, `algorithm`, `payload_digest`, `container_digest` present and schema-valid on the positive case.
- **Positive verification** — DEMO-POS-001, `VERIFIED`/`VERIFIED`.
- **Integrity, structural and context rejection cases** — DEMO-NEG-001..005 (tampered payload, tampered header, invalid signature, wrong key, malformed/garbage container) and DEMO-CTX-001..002 (missing nonce, stale timestamp), all deterministic `REJECTED` with the expected reason code.
- **Contract-shaped structured results** — `VerificationResult` objects (`status`, `reason`, `claims`, `facade_version`) produced for every case in the demonstration.
- **Schema conformance against the frozen Integration Pack** — DEMO-SCHEMA-001, 8/8 structured results validated against `verification-result.schema.json` and `authenticated-claims.schema.json` from `forisec-integration-pack` @ `dba998a` / tag `v1.0.0`.
- **Managed Verification service boundary over a real loopback TCP socket** — DEMO-SVC-001..003, a real `uvicorn.Server` instance bound to an OS-assigned ephemeral `127.0.0.1` port, exercised over genuine HTTP/TCP (not an in-process mock).
- **Generic external authentication failure with internal reason restricted to audit** — DEMO-SVC-002: HTTP 401 / `AUTHENTICATION_FAILED` returned externally; the internal reason (`AUTH_UNKNOWN_CREDENTIAL`) appears only in the audit record, never in the HTTP-facing error.
- **Audit file protection and sensitive-data exclusion** — DEMO-SVC-003: audit file mode `0600`; raw credential, container base64, and a fixed list of sensitive tokens confirmed absent from the raw audit text.
- **Service-layer replay detection using Redis** — DEMO-CTX-003/004, `RedisReplayGuard` exercised directly (fresh-nonce accept, nonce-reuse reject), explicitly **not** claimed as a Core-internal capability, consistent with ADR-0002 (see §6, §7 L-07).
- **Reproducible repository, commit, tag and file anchors** — all three repository anchors (`foritech-secure-system`, `forisec-integration-pack`, `foritech-public-evidence`) verified clean before and after execution in both the manifest and the demonstration; SHA-256 digests recorded for all evidence files and freshly generated fixtures.

This document does not claim, and does not attempt to establish, anything beyond what the two source documents above already state.

## 4. Limitations-and-gaps matrix

| ID | Area/capability | M1 status | Evidence available | Missing/not demonstrated | Planned closure route | Acceptance effect |
|---|---|---|---|---|---|---|
| L-01 | Integrated final FORISEC system | `NOT M1 EVIDENCE` | Verification Core + Managed Verification demonstrated as Foritech background assets (§3) | The final cross-WP FORISEC system (RTU/WP3, Logiicdev/WP4, Solarix/WP5 outputs integrated with WP2) does not exist at M1 | Planned WP2–WP5 integration and validation activities (see §7) | No blocker for bounded TRL 5 |
| L-02 | FPGA implementation | `NOT M1 EVIDENCE` | None in M1 evidence; FPGA is an execution resource, not a separate trust authority | No completed FORISEC FPGA implementation, interface, or hardware test vectors | WP4 (Logiicdev) T4.1 (FPGA acceleration + side-channel), T4.3 (HW/SW co-design), T4.4 (benchmarking/certification evidence); external acceptance may occur in an independent test environment; inclusion in the SOLARIX pilot remains subject to a separate documented WP4/WP5 technical-compatibility and risk decision before pilot integration; no milestone identifier is assigned to that decision in the current verified work plan | No blocker for bounded TRL 5 |
| L-03 | Formal RTL model and verification | `NOT M1 EVIDENCE` | None in M1 evidence | No formal FORISEC RTL model or final proof exists | WP4 (Logiicdev) T4.5 — Formal RTL security verification (M13–M28) | No blocker for bounded TRL 5 |
| L-04 | RTU embedded implementation | `NOT M1 EVIDENCE` | None in M1 evidence beyond software-level Core/SDK evidence | Industrial embedded integration, WCET, and energy measurements on constrained hardware not demonstrated; existing software evidence is not proof of final constrained-device performance | WP3 (RTU) T3.1 (embedded signing matrix), T3.4 (deployment envelope: WCET/latency/memory/energy) | No blocker for bounded TRL 5 |
| L-05 | SOLARIX operational pilot | `NOT M1 EVIDENCE` | The relevant-environment demonstration is a real but single-host, non-production, loopback exercise — explicitly not an operational pilot (demonstration §4 "Known limitations") | No completed operational Solarix pilot; no observe-only or active-integration deployment | WP5 (Solarix) T5.1 (site assessment), T5.2 (observe-only deployment), T5.3 (active integration), T5.4 (operational validation) | No blocker for bounded TRL 5 |
| L-06 | Operational environment and multi-site deployment | `NOT M1 EVIDENCE` | Demonstration environment explicitly documented as single-host, loopback, non-production (demonstration §4) | No production SLA, fleet operation, multi-node, or multi-site operation demonstrated | WP2 T2.6 (Reference Deployment, Integrated Releases and Pilot Support); WP5 T5.6 (Sustained Pilot Operation, KPI Measurement and Evidence Collection) | No blocker for bounded TRL 5 |
| L-07 | Replay integration boundary | `PARTIALLY EVIDENCED` | Redis-backed replay behaviour demonstrated directly at the Service layer (DEMO-CTX-003/004); not wired into the Core `verify_container()` facade in this checkout (confirmed by source inspection, demonstration §10) | Integrated Core+replay pipeline; pilot-grade operational replay wiring | This is an architectural boundary defined by ADR-0002 (nonce lookup = Service layer, replay decision = Core given a supplied fact), not an implementation defect. Further operational wiring: WP5 T5.3 (active integration, live data streams) | No blocker for bounded TRL 5 |
| L-08 | ARM64/Debian/MQTT or industrial hardware | `PARTIALLY EVIDENCED` | Listed as "PASS" in a single-line "Industrial Validation" table in `foritech-public-evidence/benchmarks/BENCHMARK_RESULTS.md` (manifest §7); documentary only | No separately verifiable supporting record (raw logs, dated run, environment spec) located in the repositories checked; **not re-run or re-verified in the relevant-environment demonstration**, which was loopback-only on a development host | WP3 (RTU) T3.1/T3.4 for embedded/industrial hardware evidence | No blocker for bounded TRL 5 |
| L-09 | Production hardening and SLA | `NOT M1 EVIDENCE` | Single-node Docker/Compose deployment baseline exists per the M1 Technical Baseline (§2E); no production availability/capacity/recovery/SOC/SLA evidence | Development test results must not be converted into production performance claims | WP2 T2.6 (Reference Deployment, Integrated Releases and Pilot Support) | No blocker for bounded TRL 5 |
| L-10 | Certification and regulatory conformity | `NOT M1 EVIDENCE` | None; no certification, conformity assessment, or regulatory approval claimed anywhere in the two source documents | No certificate exists; standards/regulatory mapping work is preparation, not certification | WP3 T3.5 (certification-readiness evidence package); WP5 T5.5 (NIS2-aligned replication guide); WP6 (dissemination/certification-readiness mapping, per D6.2) | No blocker for bounded TRL 5 |
| L-11 | TRL 7 | `NOT M1 EVIDENCE` | Both `FORISEC_TRL5_EVIDENCE_MANIFEST.md` §8 and `FORISEC_TRL5_RELEVANT_ENVIRONMENT_DEMONSTRATION.md` §10 explicitly decline to adopt any repository "TRL 7 CONFIRMED" statement | No integrated TRL 7 evidence exists in either source document; any such repository statement (e.g. `foritech-public-evidence/README.md`, `kpi/KPI_TRL_MATRIX.md`) remains unresolved/superseded for proposal-baseline purposes per the manifest | Full project execution through WP2–WP5; final integrated TRL advancement is future project evidence, not current | No blocker for bounded TRL 5 |
| L-12 | Automated vulnerability detection and patching | `OUT OF PROJECT SCOPE` | Not applicable — deliberately excluded | FORISEC does not claim automated vulnerability discovery, automated patch generation, autonomous remediation, or general OTA orchestration | Not applicable — this is a scope exclusion, not a gap FORISEC promises to close. The selected subtopic-b contribution is the cryptographic provenance, authenticity, integrity and verification layer for software/firmware supply-chain evidence | No blocker for bounded TRL 5 |
| L-13 | Market adoption and commercial deployment | `NOT M1 EVIDENCE` | None; not addressed by either TRL evidence document | Engagement targets, LoIs, exploitation planning, and later adoption activities are separate from technical TRL 5 proof; no procurement, commercial adoption, or external deployment decision is guaranteed | WP6 (dissemination/exploitation), D6.2 (Exploitation Plan, incl. investor/customer adoption package) | No blocker for bounded TRL 5 |
| L-14 | External independent replication | `NOT M1 EVIDENCE` | Both source documents were prepared and executed on Foritech-owned infrastructure by an agentic assistant at the coordinator's direction; no independent organisation has reproduced the chain | Internal or agent-executed reproduction on Foritech infrastructure is explicitly **not** independent external replication | WP5 T5.5 / D5.5 (Replication Guide for Critical Infrastructure Operators, published CC BY 4.0, disseminated to a minimum of 3 operator communities) | No blocker for bounded TRL 5 |
| L-15 | Long-term maintenance and post-project operation | `NOT M1 EVIDENCE` | None in the two TRL evidence documents; any maintenance commitments live in the proposal, not in executed evidence | Maintenance plans or consortium commitments, where they exist in the proposal, are not evidence of completed post-project operation at M1 | WP2 T2.6 (platform integration and maintenance support during pilot operation, M30–M33); no verified route exists for the post-project period beyond the funded project timeline | No blocker for bounded TRL 5 |
| L-16 | Evidence durability | `PARTIALLY EVIDENCED` | The demonstration document (`FORISEC_TRL5_RELEVANT_ENVIRONMENT_DEMONSTRATION.md` §9) explicitly states the temporary JSONL audit artefacts were not retained; the signed-off document records the executed commands and observed pytest results, not the raw audit file contents | Raw, independently re-inspectable audit JSONL from the demonstration run no longer exists — only the pytest pass/fail record and the narrative description of what the audit assertions found | No verified closure route currently assigned for retaining raw temporary evidence artefacts from future demonstration runs | Qualification |

## 5. Critical-blocker assessment

| Potential issue | Critical for bounded TRL 5? | Reason |
|---|---|---|
| FPGA absent | No | FPGA is future WP4 hardware-acceleration scope, not part of the bounded Foritech provenance-to-verification chain demonstrated at M1 (L-02) |
| Operational pilot absent | No | The bounded TRL 5 chain does not require an operational pilot; the pilot is WP5 future work (L-05) |
| Formal RTL absent | No | RTL formal verification is WP4 T4.5 future work, tied to the FPGA acceleration module which is itself out of M1 scope (L-03) |
| Integrated cross-WP system absent | No | The bounded baseline is explicitly the Foritech Core/Managed Verification chain, not the full consortium system (L-01) |
| Replay implemented at Service layer | No | This is the architecturally correct boundary per ADR-0002, not a defect (L-07) |
| Temporary audit artefacts not retained | No | The demonstration's pytest pass/fail results and narrative record remain as evidence; the raw JSONL was demonstration tooling output, not itself a required deliverable artefact. Recorded as a qualification (L-16), not a blocker |
| No independent external replication | No | Independent replication is a later-stage, WP5-driven activity (D5.5); internal agent-executed demonstration is sufficient for the bounded M1/TRL 5 claim, which does not require third-party reproduction (L-14) |
| No production SLA | No | Deployment hardening and integrated release support are future WP2 T2.6 scope; the bounded baseline claims only a development/test relevant-environment demonstration (L-06, L-09) |
| No certification | No | Certification is explicitly future WP3/WP5/WP6 scope and was never claimed by either source document (L-10) |
| No TRL 7 evidence | No | Bounded TRL 5 does not require TRL 7 evidence; both source documents explicitly decline to adopt any TRL 7 claim (L-11) |

No genuine contradiction or unresolved critical failure was found in the demonstrated chain during preparation of this record. The relevant-environment demonstration reports PASS for all executed acceptance cases. The evidence manifest inventories pre-existing documentary evidence and openly records its discrepancies and qualifications; it is not itself a single test run and must not be described as reporting a 100% test-pass rate.

## 6. Claim-control rules

- **Demonstrated** means executed or frozen evidence exists at an anchored repository commit/tag, as recorded in the manifest or the relevant-environment demonstration.
- **Planned** means assigned to a future WP task, deliverable, milestone, or decision gate identified in the current verified `05_Work_Plan.md`.
- **Excluded** means deliberately outside the project scope (see L-12), not a gap awaiting future closure.
- Documentary evidence (e.g. the "Industrial Validation" table entries underlying L-08) must not be described as re-run or independently re-verified evidence.
- Component-level maturity must not be converted into a maturity claim for the complete future consortium system. The accepted baseline concerns the bounded pre-project Foritech provenance-to-verification chain; the final cross-WP FORISEC system has not yet been integrated and is therefore not assigned a completed-system TRL by this record.
- A planned pilot (WP5 T5.1–T5.4) must not be described as completed (L-05).
- A planned external assessment (e.g. FPGA acceptance in an independent test environment) must not be described as external adoption.
- FPGA acceptance in an independent external test environment must not be described as SOLARIX deployment. Inclusion in the SOLARIX pilot remains subject to a separate documented WP4/WP5 technical-compatibility and risk decision before pilot integration.
- A repository README claim (e.g. `foritech-public-evidence/README.md`'s "supporting TRL 7 deployment claims", or `kpi/KPI_TRL_MATRIX.md`'s "TRL 7 Status: CONFIRMED") cannot override the formal acceptance record; both are already flagged as unresolved/superseded in the manifest's discrepancy register (§8) and are not adopted here either.


## 7. Closure ownership and route

| Limitation ID | Responsible WP/task or decision gate | Intended evidence type | Earliest realistic closure stage | Required for bounded TRL 5? |
|---|---|---|---|---|
| L-01 | WP2/WP3/WP4/WP5 integration (cross-WP, no single task owns full integration) | Integrated system test/demonstration evidence | Late-project (after WP3/WP4/WP5 individual Results mature) | Only for later integrated maturity |
| L-02 | WP4 (Logiicdev) T4.1, T4.3, T4.4 | Hardware module, benchmark and certification evidence reports (D4.1, D4.3, D4.4) | M19 (D4.1, MS5) onward | Only for later integrated maturity |
| L-03 | WP4 (Logiicdev) T4.5 | Formal RTL Security Verification Report (D4.5) | M28 | Only for later integrated maturity |
| L-04 | WP3 (RTU) T3.1, T3.4 | Embedded PQC Signing Matrix Integration Report (D3.1, MS3); Embedded Deployment Envelope Report (D3.4) | M15 (D3.1, MS3) onward | Only for later integrated maturity |
| L-05 | WP5 (Solarix) T5.1–T5.4 | Site Assessment (D5.1), Observe-only Deployment (D5.2, MS4), Operational Validation Report (D5.4) | M12 (MS4) onward | Only for later integrated maturity |
| L-06 | WP2 T2.6; WP5 T5.6 | WP2 integrated-release, reference-deployment and pilot-support evidence (D2.6); WP5 sustained-operation and KPI evidence (D5.4) | M33 | Only for later integrated maturity |
| L-07 | Architectural — governed by ADR-0002 (already closed as a design decision); operational wiring under WP5 T5.3 | Operational replay evidence in live pilot data streams | M18–M30 (T5.3 window) | Not required — already correctly scoped |
| L-08 | WP3 (RTU) T3.1, T3.4 | Verified industrial-hardware benchmark/deployment-envelope evidence | M15–M28 | Only for later integrated maturity |
| L-09 | WP2 T2.6 | WP2 integrated-release, reference-deployment and pilot-support evidence (D2.6) | M33 | Only for later integrated maturity |
| L-10 | WP3 T3.5; WP5 T5.5; WP6 (D6.2 standardisation/certification-readiness mapping) | Certification-Readiness Evidence Package (D3.5); NIS2-aligned Replication Guide (D5.5) | M24 onward | Only for later integrated maturity |
| L-11 | Full project execution (no single task) | Integrated-system TRL re-assessment at project close | M36 (project completion, MS11) | Only for later integrated maturity |
| L-12 | Not applicable — out of scope by design | Not applicable | Not applicable | Not applicable |
| L-13 | WP6 (D6.2 Exploitation Plan) | Exploitation Plan, incl. investor/customer adoption package | M18 (interim), M36 (final) | Only for later integrated maturity |
| L-14 | WP5 T5.5 (D5.5 Replication Guide) | Third-party reproduction reports from disseminated replication guide | M33 onward | Only for later integrated maturity |
| L-15 | WP2 T2.6 (maintenance support during pilot operation) | Support/maintenance records through pilot close | M30–M33; no verified route beyond project end | Only for later integrated maturity |
| L-16 | Closure route not assigned in the current verified evidence set | — | — | Qualification only; not required for bounded TRL 5 |

## 8. Residual-risk statement

- The bounded M1 evidence (the manifest and the relevant-environment demonstration) supports only the demonstrated Foritech provenance-to-verification chain: synthetic evidence preparation, ML-DSA-65 signing/FTECH packaging, client/service submission, Managed Verification, Verification Core, and structured decision/audit evidence, all confirmed on a single-host, non-production, loopback development environment.
- This evidence does **not** establish the maturity of the complete future consortium system (integrated WP2–WP5 outputs), nor of any operational pilot, hardware acceleration, embedded deployment, or certification claim.
- Future integration and pilot claims require their own evidence, to be produced under the WP tasks and deliverables identified in §7, and must not be inferred from this record or the two documents it summarises.
- No unresolved critical technical failure was identified in the demonstrated chain during preparation of this record. This remains true as of the preparation date and repository commits stated in §1.

## 9. Conclusion

**LIMITATIONS AND GAPS RECORDED — BOUNDED TRL 5 ACCEPTANCE MAY PROCEED**

This record does not declare `TRL 5 ACCEPTED`, `TRL 7 CONFIRMED`, operational deployment, certification, commercial adoption, or independent external replication. Those determinations belong to the later `FORISEC_TRL5_ACCEPTANCE_RECORD.md` or to future project evidence produced under the WP tasks identified in §7.
