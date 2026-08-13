<!-- markdownlint-disable -->

# Hardening Report: CIFriends--typescript-action-template/1.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **CIFriends--typescript-action-template/1.0.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference external actions using mutable tags (e.g. @v4, @v3) instead of pinned full 40-character commit SHAs. This exposes the workflow to supply-chain attacks if a tag is moved to a malicious commit.

.github/workflows/ci.yml:
  - actions/checkout@v4
  - pnpm/action-setup@v3
  - actions/setup-node@v4

.github/workflows/check-dist.yml:
  - actions/checkout@v4
  - pnpm/action-setup@v3
  - actions/setup-node@v4
  - actions/upload-artifact@v4

.github/workflows/codeql-analysis.yml:
  - actions/checkout@v4
  - github/codeql-action/init@v3
  - github/codeql-action/autobuild@v3
  - github/codeql-action/analyze@v3

Locations:

- `.github/workflows/ci.yml:20`
- `.github/workflows/ci.yml:25`
- `.github/workflows/ci.yml:30`
- `.github/workflows/check-dist.yml:26`
- `.github/workflows/check-dist.yml:31`
- `.github/workflows/check-dist.yml:36`
- `.github/workflows/check-dist.yml:52`
- `.github/workflows/codeql-analysis.yml:24`
- `.github/workflows/codeql-analysis.yml:29`
- `.github/workflows/codeql-analysis.yml:34`
- `.github/workflows/codeql-analysis.yml:39`

### script-injection (severity: high)

Sub-rule (a): A GitHub Actions expression is interpolated directly inside a run: shell command. In .github/workflows/ci.yml, the step 'Get the output time' uses `${{ steps.hello.outputs.time }}` directly in a run: block: `run: echo "The time was ${{ steps.hello.outputs.time }}"`.

The expression `steps.hello.outputs.time` is a workflow-controllable value that flows through YAML template substitution before the shell processes it. If the output contains shell metacharacters, this could lead to command injection. The value should be passed via an env: variable and then referenced as a quoted shell variable instead.

Locations:

- `.github/workflows/ci.yml:57`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all unpinned action references across three workflow files by pinning to full 40-character commit SHAs (with original tags preserved as comments). Fixed script injection in ci.yml by moving `${{ steps.hello.outputs.time }}` from the run: shell command into an env: block as OUTPUT_TIME, then referencing it as $OUTPUT_TIME in the shell script.

