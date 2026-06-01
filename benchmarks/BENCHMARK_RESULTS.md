# Foritech Benchmark Results

## Environment
| Parameter | Value |
|-----------|-------|
| CPU | Intel i7-5820K @ 3.30GHz |
| OS | Ubuntu 24.04.4 LTS |
| Kernel | Linux 6.17.0-29-generic |
| Algorithm | ML-DSA-65 (FIPS 204) + ML-KEM-768 (FIPS 203) |
| liboqs | 0.14.0 |

## Performance — Local Server

| Payload | Wrap | Verify | Overhead |
|---------|------|--------|----------|
| 1 KB | 1.0 ms | 0.5 ms | 7,772 B |
| 100 KB | 1.2 ms | 0.8 ms | 7,772 B |
| 1 MB | 5.1 ms | 4.2 ms | 7,772 B |
| 10 MB | 46.6 ms | 38.9 ms | 7,772 B |

**Container overhead: 7,772 bytes — constant, payload-independent.**

## GitHub Actions (ubuntu-24.04, 2 vCPU)

| Metric | Result |
|--------|--------|
| ML-DSA-65 Sign | 0.1 ms avg |
| Full Verify | 0.1 ms avg |
| Throughput | **4,075 verifications/sec** |
| 1MB Payload | 3.2 ms |

## Industrial Validation

| Platform | Result |
|---------|--------|
| Industrial ARM64 gateway | PASS |
| Debian 12 | PASS |
| MQTT transport | PASS |
| Replay protection | PASS |
| Edge-to-cloud | PASS |

## Security Validation

| Attack | Result |
|--------|--------|
| Tampered payload | REJECTED |
| Replay attack | REJECTED |
| Malformed container | REJECTED |
| Trailing data | REJECTED |
| Algorithm downgrade | REJECTED |
| Missing signature | REJECTED |
| AFL++ fuzz crashes | **0** |

*Last updated: 2026-05-30*
