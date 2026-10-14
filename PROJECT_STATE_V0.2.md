# PROJECT_STATE_V0.2

Rule: a value is VERIFIED only if the user pasted it from their own
terminal or browser. Everything else is PENDING or a DECISION.

Last updated: 2026-10-13 (Stage 0, in progress)

## Direction

- Active spec: TriageAI-v0.2-Specification.md, "Modest Upgrade",
  revision 2. F1 evidence-linked AI statements, F2 --no-ai,
  F3 versioned JSON report (Should).
- Parked for a later release: SOC Realism v1.2 (nested Wazuh adapter,
  JSONL, provenance, case packages, AUTH-002). It needs real sanitized
  lab exports and a re-baseline against the v0.2.0 tag.
- Working branch: feature/v0.2-evidence-linked-ai
- Decision source: user said "let's start with it" in reply to the
  recommendation to take the Modest spec first.

## Verified baseline

- v0.1.0 tag object: 49f255f05190757073211b6acbe7602368bed88a
- v0.1.0 tag commit: 7c1aa86e6253dda3ff23c510bce0cf74da01c036
  (local rev-parse and remote peeled tag line agree)
- Branch base: 947c580dda031ddb63817ecadb2edf4243dc6349
  A web-editor commit, 2026-10-09, README.md only (1 file changed),
  author jatintrace noreply address.
- origin: https://github.com/jatintrace/triageai.git
- git grep for the old username on origin/main: no matches
- v0.1 results at 7c1aa86 (pasted 2026-10-09): 424 passed, 7 skipped;
  Ruff clean; strict mypy clean on 27 files; pip-audit no known
  vulnerabilities

## PENDING (Stage 0 stays open until these are filled)

- [ ] User confirms they authored commit 947c580
- [ ] git diff --stat v0.1.0 HEAD lists only README.md
- [ ] pytest, Ruff, mypy, pip-audit re-run at 947c580:
- [ ] CI, all four legs, commit 7c1aa86:
- [ ] CI, all four legs, commit 947c580:
- [ ] Last pytest line, ubuntu-latest 3.11, commit 7c1aa86:
- [ ] Last pytest line, windows-latest 3.11, commit 7c1aa86:
- [ ] Commit identity decided (name and email):

## Stage 1 decisions needed from the user

1. Ship F3 (JSON report) in 0.2? Recommended: yes, if F1 and F2 are
   done by the halfway point.
2. Duplicate entity values: reject (recommended) or deduplicate.
3. FQDN trailing dot: keep distinct (recommended).
4. Heuristic scope change F1-R34 (supplied package only): accept
   (recommended).

## Stage 1 spec corrections to fold in

- Handle order must be timeline order on the PRE-redaction case, with
  the record-ID to handle map built there and carried with the
  redacted case. Redaction rewrites event IDs but not matched IDs.
- A01 needs a mechanism: a snapshot taken before any change, built
  from build_cases output per fixture.
- Name every v0.1 test that must change and why.
- Split spec step 3 into four stages: package, parser, validator,
  switch-over.
- Record IDs leave the prompt. State it.
- Single source for the version string.
- Check the bytes of a PowerShell-redirected file before documenting
  it. Windows PowerShell 5.1 may write UTF-16.
- 256-character entity cap makes long Windows paths unclaimable.
  Document it.
- Report schema_version is a string and the draft schema_version is
  an integer. Consider renaming one.

## v0.2 docs backlog

- ACCEPTANCE.md cites the streaming limit as LIMITATIONS item 14. It
  is item 13 (seen in the 7c1aa86 diff).
- ACCEPTANCE.md says 6 symlink tests on Ubuntu but 7 skips on
  Windows. Confirm the count and fix the wording.
- README at 947c580 dropped content from the 7c1aa86 version:
  architecture diagram, AI trust-boundary section, CI and platform
  statement, fixture outputs, links to the soc-lab and secureguard
  repositories. Whether any returns below the part seen is unknown.
  Ask for the current file before rewriting it.
- The soc-lab and secureguard repositories are outside this repo and
  may still hold old-username links.

## Stage log

- Stage 0: IN PROGRESS