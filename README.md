Throwaway repo for authorized security research against jdx/mise
(GHSA-w4h9-vq76-98p4: cached-install signature/checksum verification bypass).

This repo tests one specific open question the mise maintainer raised when
closing that advisory as Low: can a low-privileged/untrusted-triggered
workflow write a GitHub Actions cache entry that a separate, more-privileged
workflow later restores and trusts, such that mise's confirmed `is_version_installed`
bypass lets a tampered binary run with the privileged job's permissions?

Workflows:
- `pr-write-realistic.yml` (trigger: `pull_request`) — installs a tool via
  mise, tampers the installed binary in place, then tries to save it to the
  Actions cache under key `mise-boundary-poc-realistic-v1`.
- `pr-target-write-worstcase.yml` (trigger: `pull_request_target`, with
  `cache-mode: write` explicitly declared, i.e. the deliberate opt-out of
  GitHub's default read-only-for-untrusted-triggers protection) — same tamper,
  saved under key `mise-boundary-poc-worstcase-v1`.
- `push-restore.yml` (trigger: `push` to `main`) — tries to restore both cache
  keys and, on any hit, runs `mise install --locked` (with
  `MISE_PARANOID=1`, `MISE_LOCKED_VERIFY_PROVENANCE=1`) against the restored
  directory and reports what actually ran.

Not a real tool, not a real production workflow. Safe to delete after the
PoC is captured. See the writeup for results.
trigger note: opened to fire pull_request and pull_request_target cache-write workflows
round 2 trigger
