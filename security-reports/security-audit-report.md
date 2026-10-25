# TechVision AI — Security Scan Results Report

**Project:** AI Startup Security Pipeline Setup  
**Assessment date:** 2026-10-22  
**Source reviewed:** `src/demo_code_1.py`  
**Automated scan status:** Pending execution  
**Scope limitation:** One demonstration Python script was provided. It may not be the separate vulnerable ML training codebase referenced in the assignment.

## 1. Executive summary
Manual source review identified three AI security risk patterns: unsafe pickle deserialization, data poisoning due to missing training-data validation, and model extraction risk through detailed prediction outputs without rate limiting or monitoring. Scanner findings and severity counts are pending; this report does not claim that an automated audit has passed.

## 2. Tools and coverage
| Tool | Purpose | Status |
|---|---|---|
| Bandit | Python security patterns including unsafe pickle use | Pending |
| Semgrep | Pattern-based static analysis and CWE classifications when supplied | Pending |
| PyLint | Code warnings/errors; not a dedicated security scanner | Pending |
| Safety | Dependency vulnerability checks | Pending |
| GitHub Actions | Automated scans and report artifacts | Workflow prepared, not tested |

## 3. Manual source-review findings
| ID | Location | Finding | Classification | Risk / caveat | Remediation |
|---|---|---|---|---|---|
| SRC-01 | Lines 29–51 | Crafted `__reduce__` returns `os.system`; `pickle.load` invokes deserialization | CWE-502 if untrusted pickle can be loaded | Potentially critical/high depending on who controls the model file and process privileges | Avoid untrusted pickle; use safer formats such as ONNX/SafeTensors where suitable; verify artifact provenance and restrict write access |
| SRC-02 | Lines 95–110 | Intentionally mislabeled samples are combined into training data without validation | ML data-integrity/poisoning risk | Impact depends on data source and model use; static tools may not detect this semantic issue | Validate provenance, labels, ranges, distributions, outliers, and trusted holdout performance |
| SRC-03 | Lines 147–179 | Prediction method returns full probabilities; demo simulates repeated queries without rate limiting/monitoring | Model extraction/API abuse risk | The supplied file simulates an API locally; no actual network service is shown | Minimize output, authenticate clients, add rate limits/quotas, monitor query patterns |

These are manual observations, not scanner-generated results.

## 4. Automated scan record
| Tool | Version | Result file | Status |
|---|---|---|---|
| Bandit | [record] | `bandit-results.json` | Not run |
| Semgrep | [record] | `semgrep-results.json` | Not run |
| PyLint | [record] | `pylint-results.txt` | Not run |
| Safety | [record] | `safety-results.json` | Not run |

## 5. Severity metrics
Automated severity counts are pending. Do not enter zero unless the scans complete successfully and outputs have been reviewed.
| Severity | Confirmed scanner count |
|---|---:|
| Critical | Pending |
| High | Pending |
| Medium | Pending |
| Low | Pending |
| Informational/code-quality | Pending |
| Total deduplicated confirmed findings | Pending |

## 6. Remediation order
1. Unsafe pickle deserialization: highest initial review priority if model artifacts can be supplied or replaced by untrusted parties.
2. Data poisoning: add training-data provenance, validation, label checks, and holdout evaluation.
3. Model extraction: reduce output detail, authenticate callers, add rate limiting and monitoring.

The assignment asks for ten scanner-confirmed findings. The uploaded file supports three manual observations; add other findings only if actual scan output or source evidence supports them.

## 7. CI/CD workflow
The workflow runs on pull requests, pushes to `main`/`master`, and manual dispatch. It uploads scan artifacts. Bandit HIGH and Semgrep ERROR findings are configured as blocking gates. The workflow has not yet been tested in GitHub; verify tool CLI syntax and gate behavior.

## 8. False positives
No scanner results have been reviewed, so no false positives are recorded. For each confirmed false positive, document rule, location, evidence, reviewer, and date.

## 9. Conclusion
Manual review of the supplied demo script shows three AI security risk patterns. Automated scan results, dependency findings, severity totals, and CI execution remain pending. Do not describe the project as fully audited until scans run, findings are triaged, fixes are applied, and a rescan verifies the fixes.
