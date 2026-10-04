# Foxy roadmap

This roadmap turns the [specification](specification.md) into milestones with
observable completion conditions. It has no promised dates. Investigation may
change the implementation sequence, but changes to product scope require an
explicit recorded decision.

## 0. Native baseline: complete

A full native Firefox 157.0.1 build succeeded on a modern Windows host on
2026-10-04, and a temporary-profile headless run rendered `about:buildconfig`.
The source revision was `e11529d218736fab0193a93783b649b1606ea428`.

A local Python build-monitor repair was necessary; its two regression tests
passed. Keep that repair reviewable separately from documentation and future
Windows compatibility patches.

**Evidence boundary:** the result proves a usable native build workflow. It does
not prove Windows 7 support, Foxy identity isolation, hardware graphics or
release readiness.

## 1. Establish Foxy identity and protect Firefox

Define and implement Foxy's independent application, installation and profile
identities. Audit update channels and destinations, maintenance-service identity,
registry entries, shortcuts, default-browser registration and uninstall behavior.
Use isolated profiles for baseline experiments while this work is incomplete.

**Deliverables:** an identity inventory, the corresponding implementation and a
repeatable coexistence test procedure. GitHub Issues are enabled for Foxy reports
and proposals. Adapt inherited Mozilla-specific PR automation before relying on
the pull-request workflow for external contributions.

**Completion:** ordinary interactive Foxy testing has a separate profile and
cannot accidentally target Firefox's updates or installation. When installers
and updates become available, their coexistence checks must also pass before
distribution. This milestone comes before ordinary interactive and installer
testing, rather than waiting for release branding at the end.

## 2. Establish Windows 7 feasibility and test infrastructure

Prepare recorded Windows 7 x64 test configurations. Use ESR 115 as a behavioral
and historical reference while establishing actual prerequisite updates. Audit
the new build's executable and DLL headers, imports, runtimes and dependent
components, including helpers and updater.

Investigate a current Rust compiler and standard-library configuration suitable
for Windows 7, integrated with Gecko's build system and required crates. Preserve
the upstream toolchain requirements where possible. Confirm the CPU instruction
baseline in the resulting binaries rather than assuming it from build labels.

**Deliverables:** a reproducible test matrix, binary/dependency audit, ranked
compatibility blockers and an evidenced toolchain approach. Establish independent
Foxy CI for appropriate automated checks, retaining logs and build metadata.

**Completion:** the team can reproduce failures on known configurations, explain
the foundational blockers and build the candidate compatibility configuration.
Unresolved feasibility questions remain visible; they are not hidden by feature
disabling or unsupported support claims.

## 3. Achieve Windows 7 startup and process operation

Address foundational API and runtime dependencies in small, tested patches.
Check launcher and browser startup as well as content, graphics, networking,
media and other helper processes. Verify synchronization and timing semantics
when providing older-Windows implementations.

**Deliverables:** startup and process tests, focused regression tests for fixes,
and an inventory of any security or functionality workarounds.

**Completion:** the isolated build starts reliably on the recorded Windows 7
configurations, displays and browses basic pages, and required child processes
operate. Repeat the checks on newer desktop Windows. Startup is a milestone,
not the definition of stable support.

## 4. Demonstrate full desktop functionality

Expand the feature matrix to the functionality required by the specification.
Cover Windows 7, 8, 8.1 and modern desktop Windows, with physical machines for
driver-dependent behavior. Compare with the corresponding upstream Firefox
version on modern Windows and retain test evidence.

Include hardware and software graphics paths, WebGL, fonts and DPI, media and
DRM, WebRTC, accessibility, extensions, service integrations and developer tools.
Keep external component or service blockers distinct from browser implementation
failures. Add automated regression coverage as compatibility fixes land.

**Deliverables:** a repeatable feature checklist, recorded environment matrix,
regression coverage and a maintained blocker list.

**Completion:** required features are verified on the supported desktop matrix.
Any proposed exception to full parity is discussed and reflected in the
specification before release criteria change. Publish tested prerequisites and
any justified CPU-baseline change.

## 5. Prepare the first stable desktop release

Implement and validate packaging, installers, upgrades, uninstall and recovery
from interrupted or failed updates. Establish Foxy's distribution, update host,
channels, signing configuration and release procedure. Complete Firefox
coexistence checks through the full install, run, update and uninstall lifecycle.

Review every remaining security reduction and required-feature blocker. Select
the stable upstream version for the release and rerun the required validation
after integration. Keep 157.0.1's initial milestone evidence separate from the
shipping version's evidence.

**Deliverables:** reproducible release instructions, tested update/recovery
artifacts, release notes with limitations, and source/build/test metadata.

**Completion:** the stable desktop acceptance criteria in the specification pass,
and a clean installation can be maintained through Foxy's own release process.
Promote validated work to `foxy_main` and tag releases through that process.

## 6. Validate Windows Server: later milestone

After the primary desktop release work, establish a Server test matrix for
installations with a desktop environment. Verify the same required browser,
installation, update and coexistence behavior under relevant Server policies.
Server Core is excluded.

**Deliverables:** tested Server versions, prerequisites, feature results and
documented limitations.

**Completion:** publish Server support only for configurations with evidence.
This milestone does not block the first stable desktop release.

## Continuing work: stable upstream integration

Incorporate subsequent upstream stable releases deliberately throughout
development and after release. Keep each integration's upstream revision,
compatibility patches and test results identifiable. Re-run regression tests and
the required platform checks before promotion to `foxy_main`.

Keep upstream mirror branches free of Foxy changes. Avoid unnecessary edits to
inherited documentation and tooling that increase integration conflicts.
`foxy_next` and upstream `main` remain outside the current active scope.

## Risks to investigate

| Risk | Evidence or uncertainty | Required outcome |
| --- | --- | --- |
| Native loader and runtime compatibility | Current code and dependencies use newer Windows APIs; lowering installer gates alone is insufficient. | Audit all packaged binaries and resolve unavailable imports with tested semantics. |
| Rust and C++ toolchains | The baseline toolchain has not been validated for Windows 7 runtime use. | Reproduce a suitable current-toolchain build and test its runtime dependencies. |
| Synchronization and timing | Foundation and graphics dependencies have separate newer-API paths. | Preserve wakeup, timeout, locking and time behavior with regression tests. |
| Multiprocess operation and sandboxing | Browser startup does not prove that helpers or protections work. | Validate each required process path and record protection differences. |
| Graphics, media and DRM | ANGLE, drivers, codecs and downloaded components may impose their own requirements. | Test real hardware and external components; retain blockers until resolved. |
| Independent distribution | Identity, installers, updater, signing and services still need Foxy configuration. | Demonstrate isolation and tested release/update recovery. |
| Upstream movement | Later stable releases can introduce new assumptions and dependencies. | Maintain a fixed milestone reference and repeat validation after each integration. |
| Test coverage | A few successful machines cannot establish every OS, update and driver combination. | Publish the tested matrix, preserve evidence and expand regression coverage. |

These are an initial investigation inventory, not a complete list of required
patches. Add newly discovered blockers and remove risks only when the relevant
evidence is recorded.
