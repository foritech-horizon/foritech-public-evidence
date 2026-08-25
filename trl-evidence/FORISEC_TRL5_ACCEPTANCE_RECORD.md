# FORISEC TRL 5 Acceptance Record

## 1. Document control

| Field | Value |
|---|---|
| Title | FORISEC TRL 5 Acceptance Record |
| Project | FORISEC |
| Call | HORIZON-CL3-2026-02-CS-ECCC-01 |
| Evidence owner | Foritech EOOD |
| Prepared by | Evidence record prepared from commit-anchored repository evidence |
| Approval authority | Authorised representative of Foritech EOOD |
| Preparation date | 2026-08-25 (UTC) |
| Record version | 1.0 |
| Status | `READY FOR OWNER APPROVAL` |

This record was prepared by an agentic assistant from commit-anchored repository evidence. It does not itself approve, sign, or certify anything, and no individual name, signature, or approval timestamp is asserted anywhere in this document. Formal acceptance requires completion of the owner approval block in §8 by an authorised representative of Foritech EOOD.

## 2. Acceptance subject and boundary

The acceptance subject of this record is only:

**the bounded pre-project Foritech provenance-to-verification chain**

Described compactly, this chain comprises:

- synthetic FTECH evidence/container preparation;
- authenticated metadata and claims binding;
- ML-DSA-65 signing and verification;
- Verification Core through the canonical `verify_container()` facade;
- controlled positive, integrity, structural and context decisions;
- frozen partner-facing contract and schemas (Integration Pack `v1.0.0`);
- Managed Verification service boundary;
- Service-layer replay detection;
- structured status/reason/claims and audit behaviour.

This record is **not** acceptance of the final cross-WP FORISEC system, of any operational pilot, of hardware acceleration, of embedded/industrial validation, or of any other future project Result. Those remain subject to their own future evidence, as recorded in `FORISEC_TRL5_LIMITATIONS_AND_GAPS.md`.

## 3. Frozen evidence inputs

| Evidence input | Full commit/tag anchor | Role in decision | Integrity result |
|---|---|---|---|
| `trl-evidence/FORISEC_TRL5_EVIDENCE_MANIFEST.md` | `foritech-public-evidence` commit `aedd4450d5b79a03c96b547580dd9c97a9697224` | Inventories pre-existing, already-recorded evidence for the M1 provenance-to-verification chain | Verified present, commit matches expected anchor |
| `trl-evidence/FORISEC_TRL5_RELEVANT_ENVIRONMENT_DEMONSTRATION.md` | `foritech-public-evidence` commit `f49f4c7b498a43fdea5ec6ec9c3829de63888a34` | Real, reproducible execution of the minimal chain in a relevant (loopback, non-production) environment | Verified present, commit matches expected anchor prefix `f49f4c7` |
| `trl-evidence/FORISEC_TRL5_LIMITATIONS_AND_GAPS.md` | `foritech-public-evidence` commit `71c8403595e48ae2f6dc6df371cdab23a7656134` | Scope-control record separating demonstrated evidence from planned/excluded future work | Verified present, commit matches expected anchor prefix `71c8403`; confirmed the document contains no invented milestone identifiers, no reference to drafting history, and no statement that the integrated FORISEC system holds a completed TRL 4 |
| `docs/baselines/FORISEC_M1_TECHNICAL_BASELINE.md` | `foritech-cl3-2026` commit `31ceb249a138e42a72eab6881409c31a86bcbcf8` (last commit touching this file; repository HEAD `e6c52cbaab2cf8987a888ee4d83129551d7256e3`, working tree clean) | Proposal-governance freeze of the M1 background/starting-asset boundary, used to cross-check claim boundaries in this record | Verified present, repository clean |
| `foritech-secure-system` | commit `2291f053c5beb69d170291782c5e51cdcd99764e` (HEAD, main) | Verification Core, Managed Verification, canonical fixture generator, ADRs | Verified clean working tree before and after evidence preparation |
| `forisec-integration-pack` | commit `dba998ab3b2fe43a907d4885da857a80efd72786` (HEAD, main); annotated tag `v1.0.0` (tag object `a81f05494736c9e8861abb3829b2bfa4a55d2ae7`, target commit `dba998ab3b2fe43a907d4885da857a80efd72786`) | Frozen verification contract, JSON schemas, fixtures | Verified clean working tree; tag confirmed annotated and correctly targeted |
| `foritech-public-evidence` | commit `71c8403595e48ae2f6dc6df371cdab23a7656134` (HEAD, main, immediately before creation of this acceptance-record commit) | Repository hosting all four TRL 5 evidence documents | Verified clean working tree |

This is described as a **candidate frozen evidence set**. No final repository tag has been created over these commits; tagging remains explicitly unauthorised until owner approval (see §9).

## 4. Acceptance criteria and findings

| Criterion | Required finding | Evidence | Result |
|---|---|---|---|
| A1 — Repository and evidence integrity | All relevant repositories and evidence documents are commit-anchored and clean | §3; all three repositories confirmed clean before and after evidence preparation, per the manifest §3 and demonstration §3/§8 (G1, G10) | PASS |
| A2 — Positive cryptographic verification | A valid synthetic FTECH container returns the expected verified result and authenticated claims | Demonstration DEMO-POS-001: `VERIFIED`/`VERIFIED`, claims present (`device_id`, `kid`, `nonce`, `timestamp`, `algorithm`, `payload_digest`, `container_digest`) | PASS |
| A3 — Negative and structural behaviour | Tampered payload/header, invalid signature, wrong key and garbage input return controlled expected rejections without an unhandled failure | Demonstration DEMO-NEG-001..005: all deterministic `REJECTED` with the expected reason code (`SIGNATURE_INVALID` ×4, `CONTAINER_PARSE_ERROR`); no unhandled exception or crash | PASS |
| A4 — Context/lifecycle behaviour | Missing nonce and stale timestamp cases return controlled rejection; replay behaviour is demonstrated at the Service layer in accordance with ADR-0002 | Demonstration DEMO-CTX-001 (`MISSING_NONCE`), DEMO-CTX-002 (`TIMESTAMP_SKEW_EXCEEDED`), DEMO-CTX-003/004 (Service-layer `RedisReplayGuard`, accept/reject) | PASS WITH RECORDED QUALIFICATION |
| A5 — Contract and schema conformance | All eight structured fixture results validate against the frozen Integration Pack schemas | Demonstration DEMO-SCHEMA-001: 8/8 schema-valid against `verification-result.schema.json` and `authenticated-claims.schema.json` from `forisec-integration-pack` @ `dba998a` / `v1.0.0` | PASS |
| A6 — Managed Verification boundary | Authenticated service requests produce contract-shaped results; unauthenticated access is denied generically; internal authentication reasons are confined to audit evidence | Demonstration DEMO-SVC-001 (authenticated flow), DEMO-SVC-002 (HTTP 401 / `AUTHENTICATION_FAILED`, internal `AUTH_UNKNOWN_CREDENTIAL` confined to audit) | PASS |
| A7 — Audit protection | Audit file permissions and sensitive-data exclusion were verified by the E2E tests | Demonstration DEMO-SVC-003: audit file mode `0600`; raw credential, container base64 and a fixed sensitive-token list confirmed absent from the raw audit text | PASS |
| A8 — Traceability and reproducibility | Commands, repositories, inputs, expected results, actual results and hashes are sufficiently recorded for the bounded decision | Manifest §3/§9 (repository anchors, SHA-256 digests); demonstration §6/§7/§9 (exact commands, timestamps, case matrix, SHA-256 digests) | PASS |
| A9 — Limitations disclosed | All material limitations and gaps are recorded without presenting future WP work as current evidence | `FORISEC_TRL5_LIMITATIONS_AND_GAPS.md` §4 (16-row matrix), §7 (closure ownership tied to verified WP/task/deliverable/milestone identifiers only) | PASS |
| A10 — No critical acceptance blocker | No unresolved critical technical failure exists within the bounded acceptance subject | Limitations record §5 (critical-blocker assessment, all `No`); no genuine contradiction identified during preparation of any of the three evidence documents or this record | PASS |

No underlying demonstration result has been changed, re-interpreted, or upgraded in the preparation of this record.

## 5. Recorded qualifications

1. Replay behaviour is demonstrated at the Service layer and is not claimed as Core-internal replay protection. **Accepted for bounded TRL 5** — this is the architecturally correct boundary per ADR-0002, not a defect.
2. ARM64/Debian/MQTT or industrial-hardware evidence was documentary and was not re-run in the relevant-environment demonstration. **Accepted for bounded TRL 5**.
3. Temporary Managed Verification JSONL audit artefacts were not retained; the demonstration record documents the commands and observed test results. **Accepted for bounded TRL 5**.
4. The relevant-environment demonstration is single-host, loopback and non-production. **Accepted for bounded TRL 5**.
5. No independent external organisation has reproduced the complete chain. **Accepted for bounded TRL 5**.
6. The acceptance subject is a bounded component chain, not the final integrated consortium system. **Accepted for bounded TRL 5**.

None of the above is treated as a defect in the demonstrated chain; each reflects a correctly scoped or correctly disclosed limitation of the bounded M1 evidence.

## 6. Explicit exclusions from this acceptance

| Excluded item | Reason | Future route |
|---|---|---|
| Completed FPGA implementation | No completed FORISEC FPGA implementation exists at M1 | WP4 (Logiicdev) T4.1, T4.3, T4.4 |
| Formal RTL model/proof | No formal FORISEC RTL model or final proof exists | WP4 (Logiicdev) T4.5 |
| RTU embedded and industrial-device validation | No industrial embedded integration or constrained-device performance evidence exists at M1 | WP3 (RTU) T3.1, T3.4 |
| SOLARIX operational pilot | The relevant-environment demonstration is not an operational pilot | WP5 (Solarix) T5.1–T5.4 |
| Production/multi-site operation and SLA | Demonstration environment is single-host, loopback, non-production | WP2 T2.7; WP5 T5.6 |
| Certification or regulatory approval | No certification, conformity assessment or regulatory approval is claimed | WP3 T3.5; WP5 T5.5; WP6 (D6.2) |
| Commercial adoption | No procurement, commercial adoption or external deployment decision is guaranteed | WP6 (D6.2 Exploitation Plan) |
| Independent external replication | Preparation and execution occurred on Foritech-owned infrastructure; no third-party reproduction | WP5 T5.5 / D5.5 |
| Post-project operation | Maintenance commitments in the proposal are not evidence of completed post-project operation | WP2 T2.6; no verified route beyond the funded project timeline |
| Final cross-WP FORISEC system | The integrated system across WP2–WP5 does not exist at M1 | Planned WP2–WP5 integration and validation activities |
| Integrated TRL 7 | No integrated TRL 7 evidence exists in any of the three evidence documents | Full project execution through WP2–WP5 |
| Automated vulnerability detection, automated patch generation and autonomous remediation | Intentional scope exclusion, not a future capability FORISEC promises to close | Not applicable — out of project scope by design |

For FPGA, this record uses exactly the following boundary, consistent with the limitations record:

*"FPGA acceptance in an independent external test environment must not be described as SOLARIX deployment. Inclusion in the SOLARIX pilot remains subject to a separate documented WP4/WP5 technical-compatibility and risk decision before pilot integration."*

## 7. Decision

**PREPARED DECISION: ACCEPT THE BOUNDED PRE-PROJECT FORITECH PROVENANCE-TO-VERIFICATION CHAIN AS TRL 5, SUBJECT TO FORMAL OWNER APPROVAL BELOW.**

- All ten acceptance criteria (A1-A10) in §4 have passed, or passed with a recorded qualification already accepted for bounded TRL 5 in §5.
- No critical blocker exists within the bounded acceptance subject.
- This decision does not apply to any of the excluded future capabilities listed in §6.
- This decision does not establish, and must not be read as establishing, TRL 7 for any component or for the integrated system.
- This decision becomes effective only when approved by the authorised representative of Foritech EOOD in §8 below.

No effective acceptance date is stated in this record.

## 8. Owner approval block

| Approval field | Owner entry |
|---|---|
| Decision | |
| Authorised representative | |
| Role | |
| Approval date/time (UTC) | |
| Signature or approval reference | |
| Conditions, if any | |

Until the owner approval fields are completed, this record remains `READY FOR OWNER APPROVAL` and no frozen baseline tag is authorised.

## 9. Tag authorisation rule

- Candidate tag name: `forisec-trl5-baseline-2026-08-25`.
- The tag must be annotated, not lightweight.
- The tag must point to the final acceptance-record commit, or to a later commit containing only authorised acceptance completion (i.e. a completed owner approval block).
- Tag creation is prohibited until Foritech owner approval is recorded in §8.
- Push is prohibited until a final cross-file consistency check and explicit owner instruction.
- Creation of the document commit for this acceptance record does not itself authorise tagging or pushing.

## 10. Conclusion

**ACCEPTANCE RECORD PREPARED — OWNER APPROVAL AND FROZEN TAG PENDING**
