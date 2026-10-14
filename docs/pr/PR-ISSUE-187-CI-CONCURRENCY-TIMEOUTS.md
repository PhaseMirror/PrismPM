# PR: Bounded CI Concurrency, Branch Cancellation, and Explicit Job Timeouts

**Branch**: `pr/issue-187-ci-concurrency-and-timeouts`  
**Base**: `main`  
**Fixes**: [#187](https://github.com/UOR-Foundation/PrismPM/issues/187)  
**Related**: PR #185, PR #188, PR #189  

---

## 1. Executive Summary & Dissonance Resolution

### Stated Intent
PrismPM executes rigorous, normative qualification gates across devcontainers, Lean formal proofs, conformance suites, and two-pass verification (`scripts/vv.sh`).

### Operating Reality & Contradiction
GitHub Actions concurrency is shared org-wide across `UOR-Foundation`. Workflows previously lacked job-level `timeout-minutes`, falling back to GitHub's default 360-minute (6-hour) maximum execution window. Furthermore, push concurrency groups either stacked indefinitely or assigned unique run IDs, preventing automatic cancellation of superseded commits. On 2026-10-10, 15 concurrent PrismPM jobs across 6 workflows occupied every runner slot, keeping peer repository CI (`uor-r4`, `LexLean`) in `queued` status for 6+ hours.

### Concrete Mechanism
1. **Explicit Timeouts**: Every workflow job is bounded with a specific, realistic timeout matching measured durations plus safety margin:
   - `vv.yml` (`jobs.vv`): 180 minutes (previously uncapped / 360 min).
   - `bootstrap.yml` (`jobs.native-gate`): 60 minutes.
   - `sdk.yml` (`jobs.invoke`): 60 minutes.
   - `ci-parallel.yml`: 30m (lint, models, fixtures), 45m (unit, lean, packaging), 60m (conformance).
   - `reproducibility.yml`: 45 minutes.
   - `honesty.yml`: 30 minutes.
   - `native-golden.yml`: 180 minutes (halved from 360 min).
2. **Superseded Run Cancellation**: Configured top-level `concurrency` with `group: <workflow>-${{ github.workflow }}-${{ github.ref }}` and `cancel-in-progress: true` on all branch/PR workflows so new commits immediately free occupied runner slots.
3. **Fan-Out Capping**: Enforced `strategy.max-parallel: 3` on the 6-suite conformance matrix in `ci-parallel.yml`.

---

## 2. File Changes

| File | Changes Made |
|---|---|
| `.github/workflows/vv.yml` | Added top-level `concurrency` (`cancel-in-progress: true`); bounded `vv` job to `timeout-minutes: 180` |
| `.github/workflows/bootstrap.yml` | Added top-level `concurrency` (`cancel-in-progress: true`); bounded `native-gate` to `timeout-minutes: 60` |
| `.github/workflows/sdk.yml` | Added `timeout-minutes: 60` to `invoke` job |
| `.github/workflows/ci-parallel.yml` | Set `group: ci-parallel-${{ github.workflow }}-${{ github.ref }}` with `cancel-in-progress: true`; bounded all 7 jobs (30–60m); capped conformance matrix to `max-parallel: 3` |
| `.github/workflows/reproducibility.yml` | Set branch-scoped `concurrency` with `cancel-in-progress: true`; added `timeout-minutes: 45` |
| `.github/workflows/honesty.yml` | Set branch-scoped `concurrency` with `cancel-in-progress: true`; added `timeout-minutes: 30` |
| `.github/workflows/native-golden.yml` | Halved unbounded `timeout-minutes` from 360 to 180 minutes |

---

## 3. Practical Closure Plan & Milestones

| Milestone | Deliverable | Owner | Verification Metric | Status |
|---|---|---|---|---|
| **M1: Workflow Patching** | Add explicit timeouts and concurrency cancellation across all 7 affected workflow files. | Formal Methods / CI Lead | Zero unbounded jobs in `.github/workflows/`; `grep` confirms all jobs have `timeout-minutes`. | **Complete (This PR)** |
| **M2: Concurrency Capping** | Limit matrix fan-out (`max-parallel: 3`) on conformance suites. | CI Engineer | Conformance allocates maximum 3 concurrent runners instead of 6. | **Complete (This PR)** |
| **M3: Branch Push Validation** | Push rapid consecutive commits on a test branch to verify automatic cancellation of superseded runs. | CI Engineer | GitHub Actions shows superseded run marked `cancelled` within 10s of subsequent push. | Ready for Test |
| **M4: Org Slot Latency Audit** | Verify that `uor-r4` and `LexLean` CI jobs remain unblocked during full PrismPM qualification runs. | Ecosystem Coordinator | Org runner queue latency remains < 5 minutes for peer repositories. | Scheduled Post-Merge |
| **M5: Merge & Issue Closure** | Merge PR to `main` and close Issue #187 with documented verification evidence. | Maintainer (`afflom`) | Issue #187 marked Closed on GitHub. | Target Closure |

---

## 4. Verification Gate

```bash
# Verify YAML syntax of all modified workflows
python3 -c "
import yaml, glob
for path in glob.glob('.github/workflows/*.yml'):
    with open(path) as f:
        yaml.safe_load(f)
print('All workflow YAML files parsed successfully.')
"

# Verify all jobs have timeout-minutes
python3 -c "
import yaml, glob
missing = []
for path in glob.glob('.github/workflows/*.yml'):
    with open(path) as f:
        data = yaml.safe_load(f)
    for jid, job in data.get('jobs', {}).items():
        if 'timeout-minutes' not in job:
            missing.append(f'{path} -> {jid}')
if missing:
    print('Jobs missing timeouts:', missing)
else:
    print('All jobs contain explicit timeout-minutes.')
"
```
