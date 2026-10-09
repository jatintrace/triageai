# TriageAI

An offline-first, human-in-the-loop SOC triage assistant built in Python.

TriageAI processes local JSON security-event records, groups related events
into cases, applies deterministic detection rules, and produces timelines
with analyst-review questions.

**Python establishes facts. The mock AI layer explains supplied facts.
A human analyst decides.**

> **Scope:** v0.1 is a portfolio and learning release for sanitized or
> synthetic data. It uses a deterministic mock AI provider—no real
> language model is connected. It is not a production SIEM, EDR,
> incident-decision system, or autonomous-response tool.

## What It Does

- Validates, normalizes, and deduplicates local JSON event records
- Correlates related events into cases and builds timelines
- Applies three deterministic detection rules
- Reports severity, confidence, observed facts, and rule matches
- Produces terminal or Markdown reports
- Adds mock-generated explanations, investigation questions, and
  possible false positives
- Checks draft structure, size, and selected evidence claims before
  displaying the draft

TriageAI does not execute event content, connect to live endpoints or
SIEMs, perform containment, or upload raw logs.

## Important Input Limitation

The current input schema is **flattened and Wazuh/Sysmon-inspired**.

It does not directly support the full nested structure of a real Wazuh
export. Use the included fixtures to explore the supported format.

A real Wazuh/Sysmon field-mapping layer is future work.
See [LIMITATIONS.md](LIMITATIONS.md).

## Quick Start

Requires **Python 3.11 or newer** and Git.

Clone the repository:

```bash
git clone https://github.com/jatintrace/triageai.git
cd triageai
```

### Windows — PowerShell

```powershell
python -m venv .venv
.\.venv\Scripts\python.exe -m pip install -e ".[dev]"
.\.venv\Scripts\python.exe -m triageai analyze tests/fixtures/suspicious/SC-AUTH001-bruteforce.json
```

### Linux — Bash

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -e ".[dev]"
python -m triageai analyze tests/fixtures/suspicious/SC-AUTH001-bruteforce.json
```

If Ubuntu reports that `venv` is unavailable, install the matching
Python venv package before creating the environment.

The demo reads a checked-in fixture. It requires no API key, live SIEM
connection, or external AI service.

## Example Output

Excerpt from the brute-force fixture's terminal report:

```text
Scan summary: 5 record(s) read, 0 duplicate(s) skipped, 0 undated, 1 case(s).
=== Case e337423eccc6634a ===
Severity: MEDIUM | Confidence: MEDIUM
Hosts: WIN-CLIENT02
Users: bob
Rule matches:
  - AUTH-001 (T1110): 5 authentication failures for user:bob on host WIN-CLIENT02 within 10 minutes
Observed facts:
  - 5 event(s) observed for host WIN-CLIENT02
```

The full report also includes a timeline and a mock-generated draft
explicitly marked as requiring human review.

**Interpretation:** The rule identifies a repeated-failure pattern.
It does not prove malicious intent or confirm an incident.

## CLI Usage

After installation, use your virtual environment's Python interpreter.

```text
python -m triageai analyze <file-or-directory>
python -m triageai analyze <file-or-directory> --format text
python -m triageai analyze <file-or-directory> --format markdown
```

When a directory is supplied, the reader processes eligible JSON files.

### Exit Codes

| Code | Meaning |
| --- | --- |
| `0` | Analysis completed, including when rules matched or input was valid but empty |
| `2` | Input-reading, record-validation, or normalization error |
| `3` | Command-line usage error |

A rule match does not change the exit code to a failure or confirm an incident.

## Detection Rules

| Rule | Detects | ATT&CK mapping |
| --- | --- | --- |
| **AUTH-001** | Five or more matching authentication failures within ten minutes, using independent host/user and host/source-IP dimensions | T1110 |
| **PS-001** | Recognized encoded-command indicators in PowerShell execution | T1059.001 |
| **PERSIST-001** | Scheduled-task creation through Event 4698 or recognized `schtasks /create` command-line activity | T1053.005 |

All three rules use fixed Medium severity and Medium confidence per match.

Encoded PowerShell and scheduled-task creation can be legitimate
administrative activity. These rules detect patterns or actions—not intent.

See [RULES.md](RULES.md) for required fields, detection logic,
false positives, evidence gaps, and investigation steps.

## Explore the Fixtures

| Fixture | Demonstrates |
| --- | --- |
| [Normal login](tests/fixtures/benign/normal_login.json) | A case with no deterministic rule match |
| [Repeated failures](tests/fixtures/suspicious/SC-AUTH001-bruteforce.json) | Per-user authentication-failure detection |
| [Password spray](tests/fixtures/suspicious/SC-AUTH001-password-spray.json) | Independent source-IP detection across accounts |
| [Encoded PowerShell](tests/fixtures/suspicious/SC-PS001-encoded-powershell.json) | Encoded-command detection and decoded preview |
| [Scheduled task](tests/fixtures/suspicious/SC-PERSIST001-scheduled-task.json) | Detection of task creation without inferring malicious intent |
| [Mixed signals](tests/fixtures/contradictory/mixed-signals.json) | Multiple findings within one case |
| [Duplicate JSON keys](tests/fixtures/malformed/duplicate_keys.json) | Controlled input rejection |
| [Injected instruction](tests/fixtures/prompt_injection/injected_instruction.json) | Instruction-like event text treated as data in the mock pipeline |

Run any fixture by passing its path to `analyze`.

The prompt-injection example demonstrates the current mock pipeline.
It is not evidence of resistance by a real language model.

## Architecture

```text
Local JSON input
    |
Bounded reading and strict JSON parsing
    |
Normalization, identity handling, and deduplication
    |
Correlation and deterministic detection rules
    |
Severity/confidence aggregation
    |
Render-boundary redaction
    |----------------------|
Terminal/Markdown       Prompt construction
reports                    |
                       Mock provider
                           |
                       Schema and size validation
                           |
                       Selected evidence-claim checks
                           |
                       Draft requiring human review
```

Correlation and detection operate on the original evidence.
Reports and mock-provider prompts use the render-boundary redacted
representation.

Redaction has documented limits; it is not a guarantee that arbitrary
input is safe to publish.

## AI Trust Boundary

The CLI always uses the deterministic mock provider in v0.1.

Draft validation includes:

- Required output structure and application-owned warning text
- Limits on response size, summary length, and list content
- Pattern-based checks for selected hostnames, IPv4 addresses,
  MITRE technique IDs, and contextual Windows event IDs

It does **not** validate every natural-language claim.

Specific users, processes, timestamps, IPv6 claims, and some hostname
forms are outside the current claim-validation scope.

Rejected drafts produce a fixed rejection reason without replacing
the deterministic findings or displaying raw provider error text.

See [LIMITATIONS.md](LIMITATIONS.md) and
[THREAT_MODEL.md](THREAT_MODEL.md) for exact boundaries and residual risks.

## Testing and Development

After installing the development dependencies:

```text
python -m pytest -q
python -m ruff check .
python -m mypy src
python -m pip_audit
```

On Windows, use `.\.venv\Scripts\python.exe` in place of `python`
if the environment is not activated.

The [CI workflow](.github/workflows/ci.yml) is configured for Ubuntu
and Windows with Python 3.11 and 3.13.

See [ACCEPTANCE.md](ACCEPTANCE.md) for the recorded v0.1 verification
results and release decision. Platform-dependent tests may be skipped
on an individual operating system.

Security-focused tests cover input handling, redaction, report rendering,
path handling, prompt boundaries, draft validation, and rejection paths.
Passing tests demonstrate the tested cases, not universal security.

## Known Limitations

Key boundaries include:

- No direct mapping of real nested Wazuh exports
- No connected language model or live SIEM integration
- Partial, pattern-based AI-claim validation
- Redaction does not recognize every secret format
- Simplified command-line tokenization
- Fixed correlation strategy
- Remaining platform-dependent file-opening race risks

The complete list is maintained in [LIMITATIONS.md](LIMITATIONS.md).

## Project Documentation

- [Rule catalogue](RULES.md)
- [Threat model](THREAT_MODEL.md)
- [Acceptance record](ACCEPTANCE.md)
- [Known limitations](LIMITATIONS.md)
- [Security policy](SECURITY.md)
- [Privacy](PRIVACY.md)
- [Changelog](CHANGELOG.md)
- [Release checklist](RELEASE_CHECKLIST.md)

## Related Projects

- **[SOC Lab](https://github.com/jatintrace/soc-lab):**
  Hands-on detection testing and investigation documentation.
- **[SecureGuard](https://github.com/jatintrace/secureguard):**
  Local static-analysis tooling for Python and PHP.
- **[ProofSentinel](https://github.com/jatintrace/proofsentinel):**
  An early-development security testing and evidence harness.

## License

MIT — see [LICENSE](LICENSE).
