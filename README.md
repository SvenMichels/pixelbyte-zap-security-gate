# PixelByte ZAP Security Gate

This repository provides a practical DevSecOps tool:  
an automated OWASP ZAP Baseline Scan integrated into a GitHub Actions workflow.

It serves as a security gate for web applications by scanning a target URL for common vulnerabilities and producing a detailed report.  
Part of the PixelByte DevSecOps Toolkit  
Engineering the future of cyber defense.

## What this tool does

Runs an automated Dynamic Application Security Test using OWASP ZAP.  
Scans any given URL for common web vulnerabilities.  
Generates a downloadable HTML report as a GitHub Actions artifact.  
Can fail the pipeline when critical issues are identified, depending on configuration.  
Uses custom thresholds defined in `zap/zap-rules.conf`.

## How it works in this repository

Open the Actions tab and select `PixelByte OWASP ZAP Baseline Scan`.  
Use `Run workflow` to start a scan.  
Provide the URL you want ZAP to evaluate, for example a staging or demo environment.  
The GitHub Action uses the official `zaproxy/action-baseline` integration.  
A ZAP Baseline Scan is executed against the provided target.  
The scan evaluates thresholds defined in `zap/zap-rules.conf`.  
The generated report is uploaded as an artifact named `zap-report`.

If a rule marked as `FAIL` is triggered, the job terminates with a failed status and effectively blocks the deployment.

## Repository structure

```text
pixelbyte-zap-security-gate/
├─ .github/workflows/
│  └─ zap-baseline.yml     # GitHub Action with ZAP scanner
├─ zap/
│  ├─ zap-rules.conf       # Custom rule thresholds and fail conditions
│  └─ README-zap-notes.md  # Notes for tuning and experiments
└─ README.md               # Documentation
Reusable workflow for other repositories
This repository is designed so that other projects can use the ZAP Security Gate as a reusable GitHub Actions workflow.

The workflow file ./github/workflows/zap-baseline.yml supports both manual runs in this repository and workflow_call from external repositories.

Example definition inside zap-baseline.yml:

yaml
Code kopieren
name: PixelByte OWASP ZAP Baseline Scan

on:
  workflow_dispatch:
    inputs:
      target:
        description: 'Target URL to scan'
        required: true
        type: string

  workflow_call:
    inputs:
      target:
        required: true
        type: string

jobs:
  zap-scan:
    runs-on: ubuntu-latest
    steps:
      - name: Run ZAP Baseline Scan
        uses: zaproxy/action-baseline@v0.12.0
        with:
          target: ${{ inputs.target }}
          cmd_options: '-a'
          rules_file_name: zap/zap-rules.conf
The important part for external usage is the workflow_call section and the use of inputs.target inside the job configuration.

How to use this Security Gate in another repository
To integrate this security gate into a different repository, create a workflow file such as .github/workflows/zap-from-pixelbyte.yml in that repository.

Example:

yaml
Code kopieren
name: Run ZAP via PixelByte Security Gate

on:
  workflow_dispatch:
    inputs:
      target:
        description: 'Target URL to scan'
        required: true
        type: string

jobs:
  zap:
    uses: SvenMichels/pixelbyte-zap-security-gate/.github/workflows/zap-baseline.yml@v1
    with:
      target: ${{ inputs.target }}
The uses line references this repository and the reusable workflow.
The version after the @ should point to a tag or release in this repository, for example @v1.0.0.

After adding this file, the consuming repository can open the Actions tab, select Run ZAP via PixelByte Security Gate, provide a target URL and trigger the scan. The scan will run through the PixelByte ZAP Security Gate workflow and upload the ZAP report as an artifact in that repository.
