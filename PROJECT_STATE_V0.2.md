# PROJECT_STATE_V0.2

Rule: a value is VERIFIED only if the user pasted it from their own
terminal or browser. Everything else is PENDING or a DECISION.

Last updated: 2026-10-14 (Stage 1 in progress)

## Direction

- Active spec: docs/SPEC_V0.2.md (Modest Upgrade, revision 2) plus
  docs/SPEC_V0.2_ADDENDUM.md. The addendum wins on conflict.
- Parked for a later release: SOC Realism v1.2 (nested Wazuh adapter,
  JSONL, provenance, case packages, AUTH-002). It needs real sanitized
  lab exports and a re-baseline against the v0.2.0 tag.
- Working branch: feature/v0.2-evidence-linked-ai
- Decision source: user said "let's start with it" in reply to the
  recommendation to take the Modest spec first.

## Working rules

1. Command output is labelled expected until the user pastes it.
2. Before editing an existing file, ask for its current content.
3. No test is deleted or weakened to pass a stage. Reversals required
   by the contract follow AD-04.
4. A stage closes only with pasted pytest, Ruff, strict mypy,
   pip-audit, git diff --cached --check, git status, the push, and the
   CI conclusion of the pushed HEAD (all four jobs).
5. Run git status before every git add.
6. One stage per message.

## Verified baseline

- v0.1.0 tag object: 49f255f05190757073211b6acbe7602368bed88a
- v0.1.0 tag commit: 7c1aa86e6253dda3ff23c510bce0cf74da01c036
  (local rev-parse and remote peeled tag line agree)
- Branch base: 947c580dda031ddb63817ecadb2edf4243dc6349, a
  web-editor commit of 2026-10-09 that changes README.md only. Code
  at 947c580 equals v0.1.0 (git diff --stat v0.1.0 HEAD, pasted
  2026-10-14).
- origin: https://github.com/jatintrace/triageai.git
- Results at 947c580 (pasted 2026-10-14): 424 passed, 7 skipped;
  Ruff clean; strict mypy clean on 27 files; pip-audit clean.
- Commit identity: the GitHub noreply address of the jatintrace
  account, set in repository-local git config. v0.1 commits keep their
  original author.
- Branch commits: 8e5f0e5, 22b8935, 32b3a29 (pushed with upstream
  tracking).

## CI evidence (GitHub API output)

- Runs 20 through 34: all completed, success (pasted 2026-10-14).
- Run 31 (7c1aa86, main) and run 33 (947c580, main): four jobs each,
  all success.
- Run 35 (32b3a29, this branch): four jobs, all completed / success,
  found by matching the SHA of HEAD (pasted 2026-10-14).
- git grep for continue-on-error under .github: no matches.

## Open items

- [ ] Authorship of commit 947c580 is unconfirmed by the user.
      Exposure is README text only. Re-ask at Stage 12 before the
      release record is written.
- [ ] Current README content is needed before the docs rewrite
      (AD-10).
- [ ] Old-username links may remain in the soc-lab and secureguard
      repositories (outside this repository).

## Decisions

D1 to D4 are recorded in the addendum, section 1. Committing the
addendum accepts them.

## Stage log

- Stage 0: CLOSED. Baseline, tag, identity and CI verified.
- Stage 1: IN PROGRESS. Contract lock.