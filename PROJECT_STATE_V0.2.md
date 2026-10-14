# PROJECT_STATE_V0.2

Rule: a value is VERIFIED only if the user pasted it from their own
terminal or browser. Everything else is PENDING or a DECISION.

Last updated: 2026-10-14 (Stage 0, one confirmation pending)

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
  A web-editor commit, 2026-10-09, README.md only, author is the
  jatintrace noreply address. Authorship not yet confirmed by user.
- origin: https://github.com/jatintrace/triageai.git
- git grep for the old username on origin/main: no matches
- v0.1 results at 7c1aa86 (pasted 2026-10-09): 424 passed, 7 skipped;
  Ruff clean; strict mypy clean on 27 files; pip-audit clean
- Code at 947c580 equals v0.1.0: git diff --stat v0.1.0 HEAD lists
  README.md only, 218 insertions, 322 deletions (pasted 2026-10-14)
- Results at 947c580 (pasted 2026-10-14): 424 passed, 7 skipped;
  Ruff clean; strict mypy clean on 27 files; pip-audit clean
- Commit identity: commits on this branch are authored as
  "Jatin Gupta" with the GitHub noreply address, set in the
  repository-local git config. v0.1 commits keep their original
  author and are not rewritten.
- Branch feature/v0.2-evidence-linked-ai was created from 947c580 and
  pushed with upstream tracking on 2026-10-14. The state file's first
  commit was amended before the first push, so it is 8e5f0e5, not
  d22cc44. The second commit is 22b8935.

## CI evidence (GitHub API output, pasted 2026-10-14)

- Runs 20 through 34, the 15 most recent, are all completed with
  conclusion success.
- Run 31, 7c1aa86 on main: 4 jobs, all success.
- Run 32, 7c1aa86 via the v0.1.0 tag push: run-level success.
- Run 33, 947c580 on main: 4 jobs, all success.
- Run 34, 22b8935 on this branch: run-level success. The workflow
  therefore triggers on feature branches.
- Job-level detail was listed only for runs 31 and 33. Per-step
  results and test counts are not available from this source.

## PENDING (Stage 0 closes when these are filled)

- [ ] User confirms they authored commit 947c580 (one word)
- [ ] git grep -n "continue-on-error" -- .github returns nothing
      (confirms job success means every step passed)

## Stage 1 decisions needed from the user

1. Ship F3 (JSON report) in 0.2? Recommended: yes, with a go/no-go
   gate at Stage 10.
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
- ACCEPTANCE.md says 6 symlink tests on Ubuntu. The totals 431 =
  424 + 7 = 428 + 3 point to 7. Confirm against the test file.
- README at 947c580 dropped content from the 7c1aa86 version:
  architecture diagram, AI trust-boundary section, CI and platform
  statement, fixture outputs, links to the soc-lab and secureguard
  repositories. Whether any returns below the part seen is unknown.
  Ask for the current file before rewriting it.
- The soc-lab and secureguard repositories are outside this repo and
  may still hold old-username links.

## Stage log

- Stage 0: code baseline and CI verified. Authorship confirmation
  pending. Closes with the Stage 1 state-file update.