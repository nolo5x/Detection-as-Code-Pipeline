# Detection-as-Code Pipeline

A hands-on Detection-as-Code project that demonstrates how Sigma detection rules can be validated through CI/CD and converted into Splunk SPL.

## Project Overview

This project implements a basic detection-engineering workflow using Sigma, Splunk, Git, GitHub, and GitHub Actions. Detection logic is stored as code, validated automatically, and converted into Splunk-compatible searches.

The project also demonstrates CI/CD testing by intentionally introducing an invalid Sigma condition, confirming that the pipeline rejects it, correcting the rule, and verifying that the workflow passes.

## Objectives

- Store detection logic as version-controlled Sigma rules
- Validate Sigma rules before deployment
- Convert Sigma detections into Splunk SPL
- Use GitHub Actions for automated CI validation
- Demonstrate a failed CI check caused by invalid detection logic
- Correct the detection and verify a successful CI run
- Use a branch and pull-request workflow for detection changes

## Technologies Used

- Sigma
- sigma-cli
- Splunk / Splunk SPL
- Git
- GitHub
- GitHub Actions
- YAML
- macOS Terminal / zsh

## Repository Structure

```text
Detection-as-Code-Pipeline/
├── .github/
│   └── workflows/
│       └── detection-ci.yml
├── detections/
│   └── windows/
│       └── powershell/
│           └── invoke_webrequest.yml
├── splunk/
│   └── generated/
│       └── invoke_webrequest.spl
├── docs/
│   └── ci-cd-testing.md
├── .gitignore
├── requirements.txt
└── README.md
```

## Detection Rule

The primary Sigma detection identifies PowerShell use of `Invoke-WebRequest` combined with download-related arguments such as `-OutFile` or `-Uri`.

The rule can be validated with:

```bash
sigma check detections/windows/powershell/invoke_webrequest.yml
```

It can be converted to Splunk SPL with the Splunk Windows processing pipeline:

```bash
sigma convert -t splunk -p splunk_windows   detections/windows/powershell/invoke_webrequest.yml
```

Example generated SPL:

```text
ScriptBlockText="*Invoke-WebRequest*" ScriptBlockText IN ("*-OutFile*", "*-Uri*")
```

## CI/CD Pipeline

The GitHub Actions workflow validates Sigma rules whenever relevant changes are pushed or submitted through a pull request.

The project tested both sides of the CI process:

1. A deliberately invalid Sigma condition was introduced.
2. Local Sigma validation detected the undefined condition.
3. The test branch was pushed to GitHub.
4. GitHub Actions correctly failed the pull-request check.
5. The Sigma condition was corrected.
6. The updated commit triggered CI again.
7. The GitHub Actions check passed.
8. The pull request was successfully merged into `main`.

This demonstrates that invalid detection logic can be caught before it is accepted into the primary branch.

## Detection Engineering Workflow

```text
Write Sigma Rule
      |
      v
Validate Rule
      |
      v
Commit to Git
      |
      v
Push / Pull Request
      |
      v
GitHub Actions CI
      |
      +---- Validation Fails ---> Fix Detection ---> Re-run CI
      |
      v
Validation Passes
      |
      v
Merge
      |
      v
Convert to Splunk SPL
```

## What This Project Demonstrates

This project demonstrates practical experience with Detection-as-Code concepts, Sigma rule development, Splunk query generation, Git version control, CI/CD security validation, pull requests, troubleshooting failed detection rules, and automated quality checks.

## Future Improvements

Possible extensions include additional Sigma detections, automated tests against sample telemetry, ATT&CK mappings, multiple SIEM backends, linting, branch protection, and automated deployment of approved detections.

## Author

Adarsha Acharya

Cybersecurity portfolio project focused on detection engineering, SIEM workflows, and security automation.
