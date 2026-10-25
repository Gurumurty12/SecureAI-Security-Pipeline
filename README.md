# SecureAI Security Pipeline — TechVision AI

## Important
The uploaded `demo_code_1.py` is a demonstration script, not necessarily the full vulnerable ML training codebase referenced in the assignment. It has **not** been executed. It intentionally demonstrates a pickle deserialization pattern that can execute a command when unpickling; do not run it on a production machine.

## 1. Setup (Windows PowerShell)
Open PowerShell in this folder:
```powershell
py -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install bandit semgrep pylint safety numpy scikit-learn
New-Item -ItemType Directory -Force security-reports
```
Verify:
```powershell
bandit --version
semgrep --version
pylint --version
safety --version
```

## 2. Run scans
Run from the project root, one command at a time:
```powershell
bandit -r src -f json -o security-reports/bandit-results.json
semgrep scan --config auto src --json --output security-reports/semgrep-results.json
pylint src\demo_code_1.py --disable=all --enable=W,E > security-reports\pylint-results.txt
python -m pip freeze > security-reports\installed-dependencies.txt
safety scan --output json > security-reports\safety-results.json
```
Safety and Semgrep CLI options can vary by version. If a command fails, check `<tool> --help`; record a failed scan as incomplete, not as zero vulnerabilities.

## 3. Manual source observations (not scanner results)
- Unsafe deserialization: the script creates a crafted object whose `__reduce__` returns `os.system`, then loads it with `pickle.load`. This is a CWE-502 pattern if untrusted pickle content can be loaded. Prefer safer model formats where suitable and verify model artifact provenance.
- Data poisoning: intentionally mislabeled samples are added to training data without validation. Validate data source, labels, feature ranges, distributions, outliers, and holdout performance.
- Model extraction: the simulated API returns full probability distributions and has no rate limiting or monitoring. Return only necessary information, authenticate callers, add quotas/rate limits, and monitor suspicious queries.
- The file does not itself show actual AWS credentials or a real network API. Do not claim those were found.

## 4. GitHub Actions
Workflow is `.github/workflows/security-scan.yml`. It runs on pull requests, pushes to `main`/`master`, and manual dispatch; scan artifacts are uploaded even when a scan step fails. Bandit HIGH and Semgrep ERROR findings are intended blocking gates. Test the workflow in GitHub; adjust CLI syntax for installed versions as needed.

## 5. Deliverables
- `security-reports/security-audit-report.md`
- `security-reports/remediation-priority-matrix.csv`
- `.github/workflows/security-scan.yml`
- `src/demo_code_1.py`

Do not claim the audit is complete until scans have run, results are triaged, fixes are made, and rescans verify the fixes.
