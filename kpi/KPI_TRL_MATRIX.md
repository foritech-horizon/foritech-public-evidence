# KPI vs Target-TRL-7 Threshold Matrix

**Scope note:** the rows below compare current component-level measurements (Foritech Secure System, **Starting TRL 5**) against the performance/security thresholds FORISEC targets for **integrated TRL 7**. Meeting these thresholds is necessary but not sufficient for a TRL 7 claim — TRL 7 requires demonstration of the *integrated* system in an *operational* environment (the FORISEC pilot, WP5), which has not yet occurred. See `README.md` — "TRL positioning".

| KPI | Target TRL 7 threshold | Measured (component-level, current) | Status |
|-----|-------------|---------|--------|
| Signing latency | < 1 ms | **0.1 ms** | THRESHOLD MET |
| Verification throughput | > 3,000/sec | **4,075/sec** | THRESHOLD MET |
| Container overhead | < 10,000 bytes | **7,772 bytes** | THRESHOLD MET |
| 1MB payload verify | < 10 ms | **4.2 ms** | THRESHOLD MET |
| Security attacks rejected | 6/6 | **6/6** | MET |
| Fuzz crashes (coverage-guided, Atheris) | 0 | **0** (after remediation — see `../fuzzing/`) | MET |
| Platform coverage | 2 platforms | **2 platforms** | MET |

**Status: all measured component-level KPIs meet or exceed their target-TRL-7 thresholds.** This is evidence supporting FORISEC's starting technical baseline and target feasibility — it is **not** a TRL 7 confirmation. Integrated TRL 7 is the project's target, to be demonstrated through FORISEC's embedded validation (WP3), hardware acceleration (WP4) and operational pilot (WP5).
