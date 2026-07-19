# Coverage-Guided Fuzzing — Atheris Campaign (2026-07-19)

## Methodology change

Previous fuzz evidence in this repository (`test-results/TEST_SUMMARY.md`,
`benchmarks/BENCHMARK_RESULTS.md` — "AFL++ fuzz crashes: 0") was produced by
AFL++ run in **blackbox mode** (`-n` flag, non-instrumented). Re-inspection of
the raw `plot_data` from those runs showed `map_size: 0.00%` and
`edges_found: 0` for the entire duration — meaning AFL++ had no code-coverage
signal and was mutating inputs blindly against 3–6 seed files, without ever
learning which mutations reached new code paths. "0 crashes" under those
conditions is a materially weaker claim than it appears.

This campaign replaces that methodology with **Atheris** (Google, libFuzzer-based
coverage-guided fuzzing, instruments Python bytecode directly — no special
Python build required). Real branch/edge coverage is confirmed growing during
every run below.

Old AFL++ evidence is left in place (not deleted) for historical record; this
document supersedes it as the current fuzzing methodology going forward.

## Bugs found and fixed during this campaign

Coverage-guided fuzzing found 1 direct crash and, during the resulting audit,
2 further pre-existing correctness/security defects it had exposed the path
to. All three were fixed, tested, and committed before the results below were
recorded:

| # | Defect | Fix commit |
|---|--------|-----------|
| 1 | `unwrap_stream()` raised raw `struct.error` on truncated input instead of a controlled rejection; full truncation audit of all `reader.read(N)` call sites in the stream parser | `04bdaed` — Harden stream parsing against malformed input |
| 2 | Public API's `wrap_stream()` silently dropped all KEM recipients (type mismatch between API and core layers), causing DEK recovery to fail unconditionally regardless of input — this had been masking defect #3 | `065ea55` — Preserve stream recipients through public API |
| 3 | Stream KEM path used a development stub that ignored the secret key entirely (shared secret derived from public ciphertext alone) — wrong-key rejection was not actually enforced | `2291f05` — Real ML-KEM integration (now wired to the real liboqs-backed ML-KEM-768 provider) |

Full test suite after all three fixes: **523 passed, 4 skipped, 0 failed**.

## Campaign results (this run)

Each target run for ~4 hours (`max_total_time=14400`), fixes applied and
verified before this final run.

| Target | Entry point | Total execs | Coverage (edges/features) | Corpus | Crashes |
|--------|-------------|-------------|---------------------------|--------|---------|
| Container parser | `foritech.core.container_parser.load_container` | 2,656,671,687 | 22 / 22 | 10 files / 124 B | **0** |
| Stream unwrap | `foritech.core.stream.unwrap_stream` | 2,443,102,845 | 31 / 31 | 10 files / 88 B | **0** |
| Verify entrypoint | `foritech.core.verify.verify` | 255,905,038 | 97 / 132 | 27 files / 121 KB | **0** |
| **Total** | | **~5.36 billion execs** | | | **0** |

The stream target's coverage (31 edges) is higher than its pre-fix baseline
(2 edges) from the same harness, confirming the fixes above opened real new
code paths rather than just changing behavior at the entry point.

`verify` had the richest corpus growth (27 files, 121 KB) and had not yet
plateaued at the 4-hour mark — the most promising target for a longer future
run.

## Harness source

`tools/fuzz_atheris/` in the main `foritech-secure-system` repository
(private): `atheris_parser_target.py`, `atheris_stream_target.py`,
`atheris_verify_target.py`, plus `run_all.sh` / `status.sh` launcher scripts.

## Planned follow-up

Longer campaigns (12h, 24h) planned; not yet started as of this writing. This
document will be updated when those runs complete.

*Recorded: 2026-07-19*
