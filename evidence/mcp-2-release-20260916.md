# PHP Management 2.1.1 release preparation

Prepared 2026-09-16 10:24 AEST. Status: coded and verified; not merged, tagged, or published. ROOT owns the remaining native release and full coordinated acceptance gates.

## Identity and scope

- Split base: `74a85d3c03169d78380cf61533aaec1f174d9c95`, freshly fetched from `origin/main`.
- Package commit: `e40e3c7f61ff0f77f72454ff7383955b4f166a82`.
- Immutable source: `Sendmux/sendmux-sdk@fb6085ce643c117dc3f2beb29c4fc57c303272b7`, `packages/php/management/`.
- Package delta: `README.md`, `CHANGELOG.md`, `composer.json`, and `src/Configuration.php`; 18 insertions, 6 deletions, no package-file addition or removal. This evidence file is the only tracked addition.
- All 145 candidate package files match the immutable source, with one exception: `CHANGELOG.md:3` replaces `Unreleased` with `2.1.1 (2026-09-16)` following existing split release-heading style.
- Of 141 PHP source files, only `Configuration.php` changes: two generated version strings. The other 140 source files remain byte-identical to the split base. No public method, auth rule, request algorithm, or schema changed.

Canonical approach: preserve the approved immutable package and its version input from `scripts/generate-php.mjs:33`; prepare the split through the existing native package workflow. Prior source security/adoption review is recorded in immutable monorepo `evidence/mcp-2-modernisation-20260915.md:64-77`; this preparation does not reattribute older source verification to a new published artifact.

## Correctness trace

- `composer.json:11-13` carries the approved floors: Guzzle `^7.15.5`, PSR7 `^2.13.1`, and core `^2.1.1`. The manifest intentionally has no `version` or repository override.
- `README.md:19-24` gates the `^2.1.1` installation command on public Packagist availability; the complete persistent-cookie migration warning remains at line 29.
- `CHANGELOG.md:5-7` preserves the Security entry and upgrade-warning link; all earlier release history is retained.
- `src/Configuration.php:103` supplies `OpenAPI-Generator/2.1.1/PHP`; unchanged `src/Api/ConnectionApi.php:473-474` reads `getUserAgent()` into the request header.
- `src/Configuration.php:432` reports SDK package version 2.1.1 through `toDebugReport()`.
- No extra query, network call, loop, payload, cache, or shared state was added. The request path receives an updated literal value.

## Verification

A fresh isolated consumer installed the clean tracked Management candidate through its sole path repository, assigning only Management a fixture version of 2.1.1. It required exact public `sendmux/core:2.1.1` with normal Composer dependency resolution and auditing enabled; no core source/path override or old lock was used. The clean candidate snapshot excludes private worktree files.

| Gate | Result |
| --- | --- |
| Candidate and installed Management `composer validate --strict` | Both valid, exit 0. |
| Consumer `composer validate` | Valid, exit 0; expected exact core/Management pin warnings retained in `verification-run.log`. |
| Public core provenance | Installed `v2.1.1` has Git/zip reference `42769909a6c2fb34fa11657c8dce7086c00e9c25`, matching fresh Packagist metadata and all 12 previously verified public core file hashes. |
| Normal dependency resolution | Guzzle 7.15.5, PSR7 2.13.1, PHPUnit 11.5.56. |
| Platform | PHP 8.4.10 and all nine required extensions passed. |
| Dependency audit | Zero advisories and zero abandoned packages. |
| Unchanged PHPUnit inputs | `CoreTest`, `OAuthRetryTest`, and `ManagementValidationTest`: 19 tests, 55 assertions, zero errors/failures/skips. All ten test/config/helper files match immutable source bytes. |
| Runtime diagnostic | Existing public `getUserAgent()` / `toDebugReport()` probe observed RED against base, then GREEN against installed candidate, both reporting 2.1.1. |
| Installed provenance | All 145 installed Management files match the clean snapshot, worktree, and source exception; eight core classes plus four Management classes reflect to the installed consumer's vendor paths. |
| Configuration lint and diff checks | Passed. |

Runtime probe source: immutable `scripts/generate-php.test.mjs:35-42`, narrowed to Management and pointed at the relevant base/installed Configuration file. Its public calls and version parsing were retained; the full generator suite was not rerun. Observed RED, exit 1: `AssertionError [ERR_ASSERTION]`, actual `{ userAgent: 'OpenAPI-Generator/2.1.0/PHP', version: '2.1.0' }`, expected the corresponding 2.1.1 values. After importing the source, both public outputs matched 2.1.1. This is release-version sensitivity evidence, not a claim that the prior 2.1.0 package was defective.

Tests: +0, -0 tracked cases; existing tests and the source-owned runtime diagnostic were reused. Management validation checks rejection of newline-terminated email values and acceptance of a valid complete value. No assertion was weakened or skipped.

Journeys: offline installed-package verification; no browser, provider, email, or Sendmux API call. Results cover PHP 8.4.10 and these bounded cases, not a full PHP matrix, OAuth integration suite, or public Management 2.1.1 acceptance.

## Receipts and cleanup

Raw receipts are in MAIN `.claude/artifacts/mcp-2-release/`:

- `source-delta.json`, `package-delta.patch`, `source-installed-parity.json`: exact diff and hashes for all 145 Management files plus 12 public core files. Parity receipt SHA256: `7d6241dadcf378f7185c4f872890278e32a1d77beae7c344ddc6a6eab9b6e827`.
- `consumer-install.log`, `consumer-composer.{json,lock}`, `installed-{management,core}.json`, `installed-packages.json`, `autoload-provenance.json`: normal dependency resolution and installed package identity.
- `verification-run.log`, `candidate-validation.log`, `installed-validation.log`, `consumer-validation.log`, `platform.log`, `audit.json`, `configuration-lint.log`, `phpunit-management.{log,xml}`, `test-input-parity.json`: executed checks and unchanged input proof.
- `runtime-diagnostic-source.txt`, `version-red.{log,exit}`, `version-green.json`: source probe, expected RED, and installed GREEN.
- `remote-refs-before.txt`, `packagist-management-before.json`, `packagist-public-core.json`: Management target absence and already-public core identity.
- `source-tree.tar.gz`, `candidate-package.tar.gz`, `consumer-inputs.tar.gz`: preserved immutable source, clean candidate, and consumer manifest/new lock/unchanged tests for reconstruction.
- `process-cleanup.json`, `cleanup.json`, `reaper-dry-run.txt`: exact resource handles and teardown.

Removed and verified absent: task-owned `source.2tEeeA`, `package.QlY9Xn`, and `consumer.oZHIW0` under the artifact root. All 13 recorded child PIDs and process groups are absent: `19094`, `19365`, `22496`, `22511`, `22524`, `22537`, `22550`, `23090`, `23102`, `23112`, `23116`, `23119`, `23647`. Command sessions `48629` and `51796` completed. No server, browser, container, or tunnel was created. Existing primary files and artifacts were preserved; no earlier worktree was removed.

The release worktree remains for its PR. MAIN retains head `394cd80a003c9f961770da9238ddda9b1e95744c` and its user-owned `.claude/`. The private worktree `.gitignore` and `.claude/` are not staged. Core, Sending, and SDK source checkouts were not edited.

## Review and release limits

Independent review: ready for PR preparation; 0 Critical, 0 Important, 0 Minor findings. Reviewed base `74a85d3c03169d78380cf61533aaec1f174d9c95` through package head `e40e3c7f61ff0f77f72454ff7383955b4f166a82` and the evidence draft. The reviewer independently reconciled source/archive/public-core hashes and receipts, and reran Configuration diagnostics/lint; it did not reinstall dependencies or rerun the full suite. Report preserved at `.claude/artifacts/mcp-2-release/independent-review.md`, SHA256 `5d151e67606e120d8a1f8b65956bda0b20b99171000803264936dd2148880806`. No `major` label is warranted by this actual four-file metadata/version diff. ROOT independently read the package diff and found no issue.

At preparation, Management remote `main` and Packagist `v2.1.0` both identify the base above; target tag/version 2.1.1 are absent. ROOT must settle live reviews/checks, merge/tag the exact SHA, prove public Packagist version/source ingestion, and run the published Management consumer. Core's public proof and this candidate check do not substitute for those remaining Management gates. Full coordinated app/manual acceptance remains outside this delegation.

Parked: none within this package delta.
