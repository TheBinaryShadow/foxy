# Contributing to Foxy

Foxy follows stable Firefox releases and aims to provide full Firefox
functionality on x86_64 Windows 7 and newer. Compatibility work is at an early
stage: a native Firefox 157.0.1 baseline builds and starts on modern Windows,
but Windows 7 support and Foxy's independent application identity are not yet
implemented or verified.

Start with the [project documentation](docs/foxy/index.md),
[specification](docs/foxy/specification.md), and [roadmap](docs/foxy/roadmap.md).
The [development guide](docs/foxy/development.md) covers MozillaBuild, native
builds, testing, and branch roles. Coding agents must also follow
[AGENTS.md](AGENTS.md) and applicable component instructions.

## Proposing and implementing changes

Report problems and proposals through
[Foxy Issues](https://github.com/TheBinaryShadow/foxy/issues). The intended code
review workflow uses this repository's pull requests, but inherited Mozilla PR
automation still needs adaptation before public pull-request intake can be
relied on. This is tracked in the roadmap.

For a large change, describe the problem, proposed behavior, and compatibility
implications before implementing it. State whether the work addresses an agreed
milestone or proposes a scope change.

Base changes on `foxy_dev`. Once the project contribution workflow is ready,
open pull requests against `foxy_dev`.
`foxy_main` is the project's default branch and receives changes after review
and appropriate validation.

Keep changes focused and preserve upstream coding conventions. Separate Foxy
compatibility changes from unrelated cleanup so that reviewers can understand
the behavior and future upstream updates remain manageable. Add comments only
when they explain otherwise unclear code, and preserve existing comments unless
the change makes them obsolete.

## Validation and review

Run the checks appropriate to the change: formatting, applicable linters,
focused regression tests, and a native build when compiled behavior changes.
Documentation-only changes need documentation validation rather than a full
browser rebuild. See the [validation commands](docs/foxy/development.md#local-validation).

In a pull request, explain:

- The problem and the resulting behavior.
- Relevant upstream version and compatibility implications.
- The checks performed and their results.
- Any untested platforms, unresolved failures, or changes to security protections.

Foxy's own CI is planned. Mozilla Try, Lando, Bugzilla, and Phabricator are
upstream contributor services; submitting Foxy work there is not part of the
default workflow. Any separate upstream contribution needs an explicit decision
and the appropriate access.

## Reporting problems and compatibility results

Use a fresh test profile and record reproducible steps. Include:

- The exact Foxy commit, upstream Firefox version, build configuration, and
  whether the build is local or distributed.
- Windows edition, architecture, version, service pack, build number, and
  relevant installed updates.
- CPU model, GPU model, and driver version.
- Expected and actual behavior, relevant logs, and the test case or website.
- Results from a comparison browser on the same machine, when available.

For a compatibility report, name the features actually tested and distinguish
working, failing, and untested behavior. A successful launch does not establish
full platform support. Headless rendering also does not establish hardware GPU,
media, installer, update, or accessibility compatibility.

Until the independent identity and coexistence milestone passes, use the
development guide's temporary-profile procedure. Do not reuse a normal Firefox
profile, install test builds over Firefox, or register them as the default
browser. Avoid including browsing history, credentials, or other personal data
in public reports.

## Upstream code and licensing

Retain existing license headers and third-party notices. The repository's
[LICENSE](LICENSE) and applicable component licenses continue to govern their
code. Refer to upstream functions, classes, preferences, and flags by name in
reports; use verified Searchfox permalinks when a source location is helpful.
