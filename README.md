# PixelByte ZAP Security Gate

This repository provides a practical DevSecOps tool:
an automated **OWASP ZAP Baseline Scan** integrated into a GitHub Actions workflow.

It acts as a **security gate** for web applications by automatically scanning
a target URL for common web vulnerabilities and producing a detailed report.

> Part of the PixelByte DevSecOps Toolkit –  
> "Engineering the future of cyber defense."

---

## What this tool does

- Runs an **automated Dynamic Application Security Test (DAST)** using OWASP ZAP
- Scans any given URL (`target`) for common web vulnerabilities
- Generates a downloadable **HTML report** as a GitHub Actions artifact
- Can **fail the pipeline** when critical issues are found (configurable)
- Uses custom thresholds via `zap/zap-rules.conf`

---

## How it works

1. Go to **Actions** → "PixelByte OWASP ZAP Baseline Scan"
2. Click **"Run workflow"**
3. Enter the URL you want to scan (e.g. a staging or demo environment)
4. GitHub Actions:
    - runs the official `zaproxy/action-baseline` action
    - executes a ZAP Baseline Scan against the target
    - evaluates rules from `zap/zap-rules.conf`
    - uploads the report as an artifact (`zap-report`)

If a rule configured as `FAIL` is triggered,  
the job fails and effectively **blocks the deployment**.

---

## Repository structure

```text
pixelbyte-zap-security-gate/
├─ .github/workflows/
│  └─ zap-baseline.yml     # GitHub Action with ZAP scanner
├─ zap/
│  ├─ zap-rules.conf       # Custom rule thresholds & fail conditions
│  └─ README-zap-notes.md  # Notes for tuning and experiments
└─ README.md               # This documentation
