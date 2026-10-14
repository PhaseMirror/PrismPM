# PrismPM Issue Resolution & PR Closure Index

This directory tracks the dedicated Pull Request branches, dissonance analyses, and milestone closure plans for all open GitHub issues in `UOR-Foundation/PrismPM`.

## Summary Matrix

| Issue | Title | Dedicated PR Branch | Concrete Levers | Status |
|---|---|---|---|---|
| [#187](https://github.com/UOR-Foundation/PrismPM/issues/187) | CI hogs shared Actions concurrency | `pr/issue-187-ci-concurrency-and-timeouts` | Explicit job timeouts (30-180m), top-level cancel-in-progress concurrency, max-parallel: 3 cap | **Code Patched & Plan Defined** |
| [#69](https://github.com/UOR-Foundation/PrismPM/issues/69) | Reuse authenticated SDK exporter artifacts | `pr/issue-69-reusable-sdk-exporter-artifacts` | Generic acquisition path, closed manifest, immutable SDK inventory authentication, fail-closed bootstrap | **Plan Defined / Upstream Validated** |
| [#67](https://github.com/UOR-Foundation/PrismPM/issues/67) | Qualify portable View oracle response correlation | `pr/issue-67-portable-view-oracle-correlation` | RequestId-to-Response correlation, busy-state synchronization, first-failure diagnostic preservation, 78-case matrix | **Plan Defined / Diagnostic Qualified** |
| [#66](https://github.com/UOR-Foundation/PrismPM/issues/66) | Bound SDK lock acquisition without full images | `pr/issue-66-bound-sdk-lock-acquisition` | Bounded terminal layer capture, in-memory DiffID streaming, storage budget (<1GB), Hologram adoption | **Core Merged (#70) / Qualification Plan Defined** |
| [#63](https://github.com/UOR-Foundation/PrismPM/issues/63) | Support explicit reviewed SDK v1-to-v2 migration | `pr/issue-63-reviewed-sdk-v1-to-v2-migration` | RFC 6902 atomic test-and-replace proposal, historical v1 evidence, tamper resistance (12 mutations) | **Core Merged (#64) / Replay Plan Defined** |
| [#62](https://github.com/UOR-Foundation/PrismPM/issues/62) | Qualify modeled native application execution | `pr/issue-62-modeled-native-application-execution` | Typed effect contracts, BLAKE3 CAS durability, listener lifecycle, process reaping, token streaming | **Corpus Merged (#103) / Lifecycle Plan Defined** |

---

## Detailed PR Specifications

1. [PR: Issue #187 - Bounded CI Concurrency & Timeouts](PR-ISSUE-187-CI-CONCURRENCY-TIMEOUTS.md)  
   *(Branch: `pr/issue-187-ci-concurrency-and-timeouts`)*
2. [PR: Issue #69 - Reuse Authenticated SDK Exporter Artifacts](PR-ISSUE-69-REUSABLE-SDK-EXPORTER-ARTIFACTS.md)  
   *(Branch: `pr/issue-69-reusable-sdk-exporter-artifacts`)*
3. [PR: Issue #67 - Qualify Portable View Oracle Correlation](PR-ISSUE-67-PORTABLE-VIEW-ORACLE-CORRELATION.md)  
   *(Branch: `pr/issue-67-portable-view-oracle-correlation`)*
4. [PR: Issue #66 - Bound SDK Lock Acquisition Without Full Images](PR-ISSUE-66-BOUND-SDK-LOCK-ACQUISITION.md)  
   *(Branch: `pr/issue-66-bound-sdk-lock-acquisition`)*
5. [PR: Issue #63 - Reviewed SDK v1-to-v2 Lock Migration](PR-ISSUE-63-REVIEWED-SDK-V1-TO-V2-MIGRATION.md)  
   *(Branch: `pr/issue-63-reviewed-sdk-v1-to-v2-migration`)*
6. [PR: Issue #62 - Qualify Modeled Native Application Execution](PR-ISSUE-62-MODELED-NATIVE-APPLICATION-EXECUTION.md)  
   *(Branch: `pr/issue-62-modeled-native-application-execution`)*

---

## Verification & Governance Gate

All PR branches adhere to the Phase Mirror methodology:
- No simulation: proofs, benchmarks, and receipts are anchored to physical artifacts.
- Zero drift: toolchains pinned and validated across amd64 and arm64 targets.
- Independent adversarial review: every milestone requires verifiable cryptographic or process evidence before closure.
