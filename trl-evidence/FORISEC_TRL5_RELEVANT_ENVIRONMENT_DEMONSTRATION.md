# FORISEC TRL 5 Relevant-Environment Demonstration

## 1. Document control

| Field | Value |
|---|---|
| Title | FORISEC TRL 5 Relevant-Environment Demonstration |
| Project/Call | FORISEC / HORIZON-CL3-2026-02-CS-ECCC-01 |
| Evidence owner | Foritech EOOD |
| Executor/reviewer | Executed by Claude (agentic assistant) at the direction of, and for review by, the Foritech coordinator |
| Execution date/time (UTC) | 2026-08-25T07:31Z – 2026-08-25T07:45Z |
| Document version | 1.0 |
| Status | **DEMONSTRATION COMPLETED — FORMAL TRL 5 ACCEPTANCE PENDING** |
| Relationship to `FORISEC_TRL5_EVIDENCE_MANIFEST.md` | That manifest (commit `aedd4450d5b79a03c96b547580dd9c97a9697224`) inventoried pre-existing, already-recorded evidence and explicitly deferred "relevant-environment demonstration" as pending. This document is that demonstration: a fresh, independently executed, reproducible run against the same canonical repositories, generating new evidence rather than re-describing old evidence. It does not modify, supersede, or re-open the manifest. |

## 2. Purpose and bounded TRL scope

This document demonstrates only the minimal pre-project provenance-to-verification chain: synthetic evidence preparation → authenticated binding → ML-DSA-65 signing/FTECH packaging → client/service submission → Managed Verification → Verification Core → structured decision/audit evidence.

**Explicitly excluded from this demonstration's scope:**
- Integrated FORISEC TRL 7
- Future WP3 (RTU), WP4 (Logiicdev), WP5 (SOLARIX) Results
- Operational pilot
- Production SLA
- Formal certification
- Market readiness
- Automated patching
- Multi-site/fleet management

## 3. Repository and version anchors

| Repository | Branch | Commit/tag | Clean before | Clean after | Role |
|---|---|---|---|---|---|
| `foritech-secure-system` | main | `2291f053c5beb69d170291782c5e51cdcd99764e` (current HEAD; matches expected anchor) | Yes | Yes | Verification Core, CLI, canonical fixture generator, Managed Verification service, Redis replay guard |
| `forisec-integration-pack` | main | `dba998ab3b2fe43a907d4885da857a80efd72786`, tag `v1.0.0` (matches expected anchor) | Yes | Yes | Source of the frozen `verification-result.schema.json` and `authenticated-claims.schema.json` used for schema-conformance checks (G6) |
| `foritech-horizon/foritech-public-evidence` | main | `aedd4450d5b79a03c96b547580dd9c97a9697224` (matches expected manifest-commit anchor; this demonstration adds a new commit on top, see §Commit) | Yes | Yes | Destination repository for this demonstration record |

All three anchors matched the values expected before execution (see the task's "Expected anchors" list); nothing had to be corrected.

## 4. Relevant environment

| Property | Value |
|---|---|
| Host/platform | `fori-tech-x999` (bounded description; hostname/IP not otherwise disclosed) |
| OS | Ubuntu 24.04.4 LTS |
| Kernel | Linux 7.0.0-28-generic, x86_64 |
| CPU/architecture | x86_64 |
| Python | 3.12.3, existing project `.venv` at `foritech-secure-system/.venv` |
| liboqs-python | 0.14.0 |
| Managed Verification runtime | `foritech-managed-verification` 0.1.0, `foritech-verification-client` 0.1.0, `foritech-verification-contract` 0.1.0, `foritech` (SDK) 0.5.1 — all installed editable from the current repository checkout, not from a published package index |
| FastAPI/uvicorn | fastapi 0.139.0, uvicorn 0.51.0 |
| Container/service mode | No container runtime used. The Managed Verification service was exercised via the repository's own real-socket E2E test harness (`tests/managed_verification_e2e/conftest.py`): a real `uvicorn.Server` bound to an OS-assigned ephemeral loopback port (`127.0.0.1:0`), run in a background thread of the same test process — not a mock, not `TestClient`/`ASGITransport` |
| Network boundary | Loopback only (`127.0.0.1`); no external network exposure at any point |
| Temporary port | OS-assigned ephemeral (bound via `socket.bind(("127.0.0.1", 0))`); torn down at end of test process, no persistent listener left running |
| Redis | Pre-existing local, non-production Redis instance reachable at `redis://localhost:6379` (`redis-cli ping` → `PONG`); used only for the Service-layer replay-guard case, under a demonstration-only key namespace (`foritech:replay:demo-trl5-device-001:*`), fully cleaned up (see §6, §10) |
| Synthetic-data statement | All identities, keys, credentials, nonces and payloads used in this demonstration are synthetic and throwaway: ML-DSA-65 keypairs generated fresh in-process by the canonical `tools/generate_test_fixtures.py` (device id `device-A`, `key-1`); the Managed Verification E2E harness's own synthetic credential/pepper (`e2e-test-pepper-never-a-real-secret-0x9f13`, a generated `raw_credential` string per test run); a demonstration-only replay-guard device id `demo-trl5-device-001`. No production credentials, private keys, customer data, or operator data were used at any point |
| Why relevant | This is the same code path, same Core facade (`foritech.verification.verify_container()`), same Managed Verification package, and same canonical fixture generator that produce the audited baseline evidence referenced in the manifest — executed live, in the current checkout, rather than only read as prior documentation. The real-socket E2E harness exercises a genuine HTTP/TCP round trip against a real ASGI server instance running over a real loopback TCP socket, not an in-process mock, which is the necessary minimum for a "relevant environment" claim for the client/service boundary step of the chain |
| Known limitations | (1) This is a single-host development/test environment, not a deployed or multi-node environment — see §10. (2) The Managed Verification instance was started and stopped entirely within the test harness's own process lifecycle for this demonstration; no standalone Managed Verification instance was left running as a separate long-lived process (not required by the task, and avoided deliberately as an unnecessary persistent service). (3) No industrial ARM64/Debian/MQTT hardware was exercised in this demonstration — that class of evidence, where it exists, is documentation-only per the manifest (§7 of the manifest) and is not re-claimed here |

## 5. Demonstration topology and chain

`Synthetic evidence → authenticated binding → ML-DSA-65 signing/FTECH packaging → client/service submission → Managed Verification → Verification Core → structured decision/audit evidence`

| Step | Component | Repository | Version/commit | Input | Output | Evidence captured |
|---|---|---|---|---|---|---|
| 1 | Synthetic evidence/payload preparation | `tools/generate_test_fixtures.py` | foritech-secure-system @ `2291f05` | Fixed synthetic payload (`foritech-public-fixture-payload`), synthetic device id `device-A` | 8 in-memory/temp-dir `.ftech` containers + 2 throwaway ML-DSA-65 keypairs | SHA-256 of every generated file, §6/§7 |
| 2 | Authenticated metadata binding | Same generator, header assembly (`kid`, `device_id`, `timestamp`, `nonce`, `alg`) | foritech-secure-system @ `2291f05` | Header fields | JSON header bytes bound into the signed message | §7 case matrix |
| 3 | ML-DSA-65 signing / FTECH packaging | `oqs.Signature("ML-DSA-65")`, `_serialize()` in the generator | foritech-secure-system @ `2291f05`, liboqs-python 0.14.0 | Header + payload | Signed `.ftech` binary container | §7 case matrix, SHA-256 digests §6 |
| 4 | Client/service submission (positive/negative crypto+context cases) | Direct facade call, not HTTP, for this batch (see §10 qualification on scope split) | foritech-secure-system @ `2291f05` | `.ftech` bytes + verifier public key + `VerificationContext(reference_time=...)` | `VerificationResult` | §7 DEMO-POS/NEG/CTX rows |
| 4b | Client/service submission (service-boundary case) | `foritech_verification_client.ManagedVerificationClient` over real HTTP to the live uvicorn instance | foritech-secure-system @ `2291f05` | Container bytes, `project_id`, `environment`, synthetic credential | HTTP response + audit events | §7 DEMO-SVC rows |
| 5 | Managed Verification | `foritech_managed_verification` app (`app.py`, `access.py`, `audit.py`) | foritech-secure-system @ `2291f05` | Authenticated/unauthenticated HTTP request | Contract-shaped JSON response, durable audit event | §7 DEMO-SVC rows, §9 |
| 6 | Verification Core | `foritech.core.verify.verify_detailed()` via `foritech.verification.verify_container()` | foritech-secure-system @ `2291f05` | Container bytes | `(status, reason)` | §7, §9 |
| 7 | Structured status/reason/claims | `VerificationResult` (facade), validated against `forisec-integration-pack` schemas | foritech-secure-system @ `2291f05`; schemas from forisec-integration-pack @ `dba998a` | `VerificationResult` object | Schema-validated structured dict | §7, §8 (G6) |
| 8 | Audit/request evidence | JSONL audit file, `request_id`, phase sequence | foritech-secure-system @ `2291f05` | Every terminal HTTP outcome | Durable JSONL audit record | §9 |

## 6. Commands and reproducibility

### 6.1 Positive/negative/context cases (direct Core facade)

- **Working directory:** `/home/forybg/code/foritech-secure-system`
- **Environment preparation:** none beyond the repository's existing `.venv` (already provisioned; `liboqs-python 0.14.0`, `foritech` SDK 0.5.1 installed editable, verified present before running — see §4)
- **Command 1 (pytest, canonical generator + facade, self-contained):**
  ```
  .venv/bin/python3 -m pytest tests/test_public_fixtures.py -v
  ```
  Exit code: `0`. Start: `2026-08-25T07:33:32Z`. End: same second (reported duration `0.22s`). 4 passed, 0 failed.
- **Command 2 (standalone script, same generator + facade, captures per-fixture status/reason/claims/digests for the case matrix):**
  ```
  .venv/bin/python3 /tmp/forisec_trl5_demo/run_positive_negative_matrix.py
  ```
  Exit code: `0`. Start: `2026-08-25T07:34:45Z`. End: `2026-08-25T07:34:45Z`.
  Generated temporary artefacts: `/tmp/forisec_trl5_demo/fixtures/` (8 `.ftech` containers, 2 `.bin` public keys, `expected_claims.json`) — outside the repository tree, not committed anywhere.
  Cleanup/retention decision: left under `/tmp` for evidence traceability during this task; not copied into any repository; `/tmp` is not part of any git working tree and is not durable/production storage.

### 6.2 Context/lifecycle — replay (nonce reuse) case

- **Working directory:** `/home/forybg/code/foritech-secure-system`
- **Environment preparation:** pre-existing local Redis (`redis-cli ping` → `PONG`); no changes made to Redis configuration
- **Command:**
  ```
  .venv/bin/python3 /tmp/forisec_trl5_demo/run_replay_demo.py
  ```
  Exit code: `0`. Start: `2026-08-25T07:35:55Z`. End: `2026-08-25T07:35:55Z`.
  This script deletes any pre-existing keys under `foritech:replay:demo-trl5-device-001:*` before running, exercises one fresh-nonce accept and one nonce-reuse reject, then deletes all keys it created and confirms zero remain.
  Generated temporary artefacts: none outside Redis; Redis keys were created and then deleted within the same run (§10).

### 6.3 Service-boundary case (real-socket Managed Verification E2E)

- **Working directory:** `/home/forybg/code/foritech-secure-system`
- **Environment preparation:** none beyond the existing `.venv` (`fastapi`, `uvicorn`, `httpx` already installed; verified present in §4)
- **Command 1:**
  ```
  .venv/bin/python3 -m pytest tests/managed_verification_e2e/test_authenticated_flow.py -v
  ```
  Exit code: `0`. Start: `2026-08-25T07:36:04Z`. End: `2026-08-25T07:36:06Z` (reported duration `0.93s`). 6 passed, 0 failed.
- **Command 2:**
  ```
  .venv/bin/python3 -m pytest tests/managed_verification_e2e/test_server_startup.py -v
  ```
  Exit code: `0`. Start: `2026-08-25T08:26:11Z`. End: `2026-08-25T08:26:12Z` (reported duration `0.73s`). 3 passed, 0 failed.
  Generated temporary artefacts: a temporary manifest, revocation overlay, and JSONL audit file under pytest's own `tmp_path`/`tmp_path_factory`, per the harness's documented design (`tests/managed_verification_e2e/conftest.py` docstring) — never created inside the repository tree, never committed, torn down automatically by pytest at process exit.
  Credentials: the harness's own synthetic pepper `E2E_PEPPER = b"e2e-test-pepper-never-a-real-secret-0x9f13"` and a generated `raw_credential` string (`e2e-{suffix}-raw-credential-value` pattern) — both test-only, never a real secret. No production credential was needed or used, since the harness manufactures its own throwaway credential per run.

### 6.4 Schema-conformance check (G6)

- **Working directory:** `/home/forybg/code/foritech-secure-system`
- **Command:** an inline Python check (via the shell tool, not saved as a repository file) that: (a) re-ran the same 8-fixture generation/verification batch as §6.1, (b) built the full contract-shaped structured result (`status`, `reason`, `claims` including `payload_digest`/`container_digest` when present, `facade_version`) for each, and (c) validated each against `forisec-integration-pack/schemas/verification-result.schema.json` with `authenticated-claims.schema.json` resolved locally via an offline `referencing.Registry` (no network fetch — the schemas' own `$id`/relative `$ref` were mapped to local file contents, since the runtime environment has no route to `foritech.bg`).
  Exit code: `0` (script printed `ALL SCHEMA VALID: True`). Start: `2026-08-25T08:26:21Z`. End: `2026-08-25T08:26:21Z`.
  An earlier attempt at this same check failed first with a missing-fields defect in my own extraction script (I had omitted `claims`/`facade_version` from the structured dict — corrected), and then with two rounds of schema `$ref` resolution failures (default `jsonschema` behaviour tried to fetch `https://foritech.bg/...` and the JSON Schema 2020-12 meta-schema over the network, which this environment cannot reach) before I built the offline registry. Both are recorded as deviations in §10 — they are failures/corrections in my own throwaway verification script, not in any Core, contract, or schema artefact under evidence.

## 7. Demonstration-case matrix

| Case ID | Input/fixture | Boundary exercised | Expected status/reason | Actual status/reason | Claims/schema result | Exit/result | Evidence | Verdict |
|---|---|---|---|---|---|---|---|---|
| DEMO-POS-001 | `valid_container.ftech` (freshly generated, synthetic `device-A`/`key-1`) | Verification Core (`verify_container`) | `VERIFIED` / `VERIFIED` | `VERIFIED` / `VERIFIED` | Claims present: `device_id=device-A`, `kid=key-1`, `nonce=fixtureNonce0001`, `timestamp=1700000000`, `algorithm=ML-DSA-65`, `payload_digest`/`container_digest` present and schema-valid | exit 0 | §6.1 Command 2 output; SHA-256 `c5fe3f96bf52573943373719cf8c68aa99f4bdcbef69e73c7c183b5d0d874a76` | PASS |
| DEMO-NEG-001 | `tampered_payload.ftech` | Core | `REJECTED` / `SIGNATURE_INVALID` | `REJECTED` / `SIGNATURE_INVALID` | claims = null (schema-valid) | exit 0 | SHA-256 `844ed20dd9a0a01b1b2bb1e70d8f047a00161763bf56f82d32fb6db0da470d9c` | PASS |
| DEMO-NEG-002 | `tampered_header.ftech` | Core | `REJECTED` / `SIGNATURE_INVALID` | `REJECTED` / `SIGNATURE_INVALID` | claims = null (schema-valid) | exit 0 | SHA-256 `896fc80c000258d4ddfc28f75c6ee93c5cfc67bd94fa2c128a56fb9c46db91ba` | PASS |
| DEMO-NEG-003 | `invalid_signature.ftech` | Core | `REJECTED` / `SIGNATURE_INVALID` | `REJECTED` / `SIGNATURE_INVALID` | claims = null (schema-valid) | exit 0 | SHA-256 `0fc532c48243f390b7c9051afd86796bf590b6eae81580b4bd1787791103251b` | PASS |
| DEMO-NEG-004 | `wrong_key.ftech` (byte-identical to valid container, verified with `wrong_key_public.bin`) | Core | `REJECTED` / `SIGNATURE_INVALID` | `REJECTED` / `SIGNATURE_INVALID` | claims = null (schema-valid) | exit 0 | SHA-256 (container) `c5fe3f96...` (same as valid), verifier key SHA-256 `796074d2ac70864e335f6b42e2ea60e93eeb4b9c81b97d9db4edc70e5ce52cb7` | PASS |
| DEMO-NEG-005 | `garbage.ftech` (`b"this is not a FTECH container at all"`) | Core (structural parse) | `REJECTED` / `CONTAINER_PARSE_ERROR` | `REJECTED` / `CONTAINER_PARSE_ERROR` | claims = null; no unhandled exception/crash | exit 0 | SHA-256 `0fd84a3e9fd93090b2064368635e3caa8866abfbc2c3c79b9e4fd3f2c03e0067` | PASS |
| DEMO-CTX-001 | `missing_nonce.ftech` | Core | `REJECTED` / `MISSING_NONCE` | `REJECTED` / `MISSING_NONCE` | claims = null (schema-valid) | exit 0 | SHA-256 `f5027d4211bd0e79e64bebe1bf979a4d0bada322781cec4a59a811ef3381d8ba` | PASS |
| DEMO-CTX-002 | `stale_timestamp.ftech` (timestamp 100,000s before `reference_time`) | Core | `REJECTED` / `TIMESTAMP_SKEW_EXCEEDED` | `REJECTED` / `TIMESTAMP_SKEW_EXCEEDED` | claims = null (schema-valid) | exit 0 | SHA-256 `e1f909b57eea6c8a95ea46a65d53ec4f94ba95a0a04b1bd3700dc7398f6a1de9` | PASS |
| DEMO-CTX-003 | Synthetic nonce `demo-nonce-first-use`, device `demo-trl5-device-001` — first use | Service-layer (`RedisReplayGuard`), **not** Core (see §10 boundary note) | Accepted (no exception) | Accepted (no exception) | N/A — Service-layer boolean check, not a contract-shaped result | exit 0 | §6.2 script output | PASS |
| DEMO-CTX-004 | Same nonce/device, second use (replay) | Service-layer (`RedisReplayGuard`) | Rejected — `ReplayAttackError("replay detected (duplicate nonce)")` | Rejected — identical message | N/A — Service-layer | exit 0 | §6.2 script output | PASS |
| DEMO-SVC-001 | Real HTTP `GET /v1/metadata`, `GET /v1/health`, `POST /v1/verify` (VERIFIED and REJECTED payloads), authenticated | Client → Managed Verification → Core, real socket | Contract-conformant response, correct `VerificationStatus`/`VerifyReason`, `facade_version="0.1"`, exactly one audit event pair per request, no extra routes | Matched on all counts (6/6 pytest assertions passed, see §6.3 Command 1) | Response shape asserted contract-conformant by the test suite itself (`metadata.api_version=="1.0"`, `contract_version=="0.1.0"`, `facade_version=="0.1"`, etc.) | exit 0 | §6.3 Command 1 pytest output | PASS |
| DEMO-SVC-002 | Real HTTP `POST /v1/verify` with an unissued credential (`this-credential-was-never-issued`) | Client → Managed Verification access-gate (Core never invoked) | HTTP 401, `AUTHENTICATION_FAILED`, runtime not invoked, internal reason (`AUTH_UNKNOWN_CREDENTIAL`) present only in audit, never in the HTTP-facing error | Matched exactly (`exc_info.value.http_status==401`, `public_error_code=="AUTHENTICATION_FAILED"`, `invocation_counter` unchanged, audit `runtime_invoked=False`) | N/A | exit 0 | §6.3 Command 1 pytest output | PASS |
| DEMO-SVC-003 | Audit file produced by the above requests | Audit/traceability layer | File mode `0600`; raw credential, container base64, and a fixed list of sensitive tokens (`pepper`, `digest_hex`, `container_base64`, `payload_digest`, `container_digest`, `device_id`, `authenticatedcontainerclaims`, any `-----BEGIN` PEM header) absent from the raw audit text | Matched exactly | N/A | exit 0 | §6.3 Command 1 pytest output (`test_audit_file_mode_and_no_sensitive_data`) | PASS |
| DEMO-SCHEMA-001 | All 8 fixtures from DEMO-POS/NEG/CTX-001..005, assembled into full contract-shaped `VerificationResult` dicts | Structured-result/schema layer | 100% schema-valid against `verification-result.schema.json` + `authenticated-claims.schema.json` | 8/8 schema-valid | — | exit 0 | §6.4 | PASS |

## 8. Acceptance-gate results

| Gate | Evidence | Result | Qualification |
|---|---|---|---|
| G1 Repository integrity | §3 — all three repositories `git status --short --branch` clean before execution | PASS | — |
| G2 Positive verification | DEMO-POS-001 | PASS | — |
| G3 Integrity rejection | DEMO-NEG-001..004 | PASS | — |
| G4 Structural rejection | DEMO-NEG-005 (`garbage.ftech`) — controlled `REJECTED`/`CONTAINER_PARSE_ERROR`, no unhandled exception | PASS | — |
| G5 Context/lifecycle behaviour | DEMO-CTX-001..004 | PASS — replay protection is correctly exercised at the Service layer in accordance with ADR-0002; no Core-level replay claim is made | Scope note: replay case (DEMO-CTX-003/004) is Service-layer only, exercised independently of the Core `verify_container()` facade — see §10 |
| G6 Contract/schema conformance | DEMO-SCHEMA-001 — 8/8 structured results schema-valid | PASS | — |
| G7 Service boundary | DEMO-SVC-001..003 | PASS | — |
| G8 Traceability | Every case in §7 links to an input/fixture, an exact command in §6, a timestamp, and an actual result; SHA-256 digests recorded for all generated fixtures | PASS | — |
| G9 No unresolved critical failure | No case in §7 has a FAIL verdict; the only non-clean items are the two self-corrected mistakes in my own throwaway schema-check script (§6.4, §10), not in any evidence artefact | PASS | Non-critical — see §10 |
| G10 Post-run integrity | §3, §9 — all three repositories confirmed clean after execution; Redis demonstration keys confirmed fully deleted (0 remaining) | PASS | — |

No gate was altered after execution to change a result.

## 9. Audit and traceability record

- **Timestamps:** every command in §6 carries an explicit UTC start (and end, where the run was not sub-second) timestamp, captured via `date -u +"%Y-%m-%dT%H:%M:%SZ"` immediately before and after each command.
- **Request/correlation identifiers:** the Managed Verification E2E flows (DEMO-SVC-001..003) each produce a per-request `request_id`, asserted by the test suite to appear consistently across every audit event for that request (`audit.events_for_request`), and to match the pattern `^[A-Za-z0-9_-]{1,64}$`. These identifiers are ephemeral, generated per test run, and were not extracted individually into this document (they exist only inside the pytest-managed temporary audit file, torn down at process exit).
- **Hashes:** SHA-256 digests for all 8 freshly generated `.ftech` containers and both throwaway public keys are recorded in §7 and were computed directly from the files under `/tmp/forisec_trl5_demo/fixtures/` immediately after generation (§6.1 Command 2).
- **Contract/schema versions:** `facade_version = "0.1"` (internal facade, ADR-0004), Integration Pack contract family `1.0` / tag `v1.0.0` (commit `dba998a`) — both confirmed live in this run's structured results (§6.4, §7 DEMO-SCHEMA-001) and via the Managed Verification metadata endpoint (`contract_version == "0.1.0"`, `facade_version == "0.1"`, §7 DEMO-SVC-001).
- **Link to manifest evidence IDs:** this demonstration exercises the same code paths as `E-CORE-001..004`, `E-FIXTURE-001..008`, `E-CONTRACT-001` (contract/schemas/fixtures) and `E-CLOSURE-001` (Managed Verification Boundary) from `FORISEC_TRL5_EVIDENCE_MANIFEST.md`, but produces independent, freshly generated results rather than re-reading those prior records.
- **Where audit evidence is located:** the Managed Verification audit JSONL file and its per-event assertions exist only inside pytest's own temporary directories for the duration of the test run (per `tests/managed_verification_e2e/conftest.py`'s documented fixture-isolation model) and are torn down automatically at process exit; they were not extracted or copied into this repository or any other durable location by this task. This signed-off demonstration record documents the executed commands and observed pytest results; the temporary JSONL audit artefacts were not retained.
- **How secrets are excluded:** no production credential, private key, or customer/operator data was used anywhere in this demonstration (§4). The one test-only secret-shaped value in play, `E2E_PEPPER`, is a hardcoded, clearly-labelled non-secret (`b"e2e-test-pepper-never-a-real-secret-0x9f13"`) baked into the repository's own test harness; it is not reproduced as a "real" credential anywhere in this document, and its exclusion from the audit file is itself one of the assertions this demonstration exercised (DEMO-SVC-003).

## 10. Deviations, failures and qualifications

- **Replay/nonce-reuse case is Service-layer only, not Core-integrated in this demonstration.** Per `docs/decisions/0002-verification-layer-boundary.md`, nonce-lookup is a Service-layer responsibility and only the resulting decision (`REPLAY_DETECTED` vs. not) is a Core concern *given a supplied fact*; a `grep` of `foritech.verification`/`foritech.core.verify` in this checkout found no call into `ReplayGuard`/`RedisReplayGuard` from the Core facade. This demonstration therefore exercises `RedisReplayGuard` directly and independently (DEMO-CTX-003/004), and does **not** claim an integrated Core+replay pipeline. This matches the task's explicit instruction not to attribute replay protection to Core where the contract assigns it to the Service layer.
- **No standalone, separately-URLed Managed Verification instance was left running.** The service-boundary case (DEMO-SVC-001..003) was exercised through the repository's own real-socket E2E harness, which starts and stops a real `uvicorn.Server` entirely within the pytest process lifecycle. This satisfies "real Managed Verification instance, non-production port, temporary configuration" as instructed, but does mean there is no separately reachable server left for independent post-hoc inspection outside of re-running the same pytest command.
- **My own schema-conformance script needed two corrections before it worked (§6.4).** First pass omitted `claims`/`facade_version` from the structured dict I built for validation — a mistake in my extraction code, not a defect in any Core, contract, or schema artefact; corrected once I checked `AuthenticatedContainerClaims`'s actual dataclass fields (`payload_digest`, `container_digest` are present and required). Second/third pass failed because default `jsonschema` `$ref` resolution tried to fetch schema URLs over the network (`https://foritech.bg/...`, plus the JSON Schema 2020-12 meta-schema), which this environment has no route to; corrected by building an offline `referencing.Registry` mapping both the schemas' `$id` values and the relative `authenticated-claims.schema.json` reference to local file contents. None of these three failures touched any repository file, any Core code path, or any existing evidence artefact — they were purely in a throwaway verification script that was never saved into any repository.
- **Industrial ARM64/Debian/MQTT hardware not exercised.** As already flagged as a manifest-level limitation (`FORISEC_TRL5_EVIDENCE_MANIFEST.md` §7), no such hardware was available or in scope for this demonstration; nothing beyond the existing documentary record is claimed here for that class of evidence.
- **No source code defect was found or fixed during this task**, per instruction; had one been found, the relevant case would have been stopped and flagged as a blocker rather than corrected in-place. No such stop was necessary — all 8 crypto/context cases and all Managed Verification E2E cases matched their documented expected results on the first genuine execution attempt (the two corrections in §6.4 were in my own script, not in the system under demonstration).

## 11. Demonstration conclusion

**DEMONSTRATION COMPLETED — FORMAL TRL 5 ACCEPTANCE PENDING**
