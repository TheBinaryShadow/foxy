# Foxy specification

Foxy aims to provide current stable Firefox functionality on x86_64 Windows,
including Windows 7. This document records the agreed product scope and the
evidence needed to claim support. The [roadmap](roadmap.md) organizes the work.

Requirements describe the intended product. They do not imply that the current
checkout already meets them.

## Current baseline

On 2026-10-04, a full native build of Firefox 157.0.1 succeeded on a modern
Windows build host. A headless run using a temporary profile rendered
`about:buildconfig`. The source baseline was
`e11529d218736fab0193a93783b649b1606ea428` on `foxy_dev`, with a local Python
build-monitor repair and regression tests.

That build retains upstream unofficial Nightly branding. Foxy's independent
identity and Windows 7 compatibility have not been implemented or verified.
Headless rendering does not establish interactive, hardware graphics, installer,
or legacy Windows compatibility.

## Platform and CPU scope

| Area | Requirement | Status |
| --- | --- | --- |
| Architecture | x86_64 only; no x86 build. | Agreed |
| Desktop Windows | Windows 7 and newer desktop editions, including Windows 8, 8.1, 10 and 11. | Target; validation pending |
| Windows 7 prerequisites | Use Firefox ESR 115 as an initial reference, then establish the required service pack, updates and runtime components through testing. | Provisional |
| Windows Server | Server installations with a desktop environment are a later support milestone and do not gate the first stable desktop release. | Planned; validation pending |
| Server Core | Excluded. | Agreed |
| CPU baseline | Start with ordinary x86_64/SSE2. Detect capabilities before using optional newer instructions. | Agreed initial baseline; build audit pending |
| Build host | Use MozillaBuild on a suitable modern Windows host; supporting Windows 7 at runtime does not require building on Windows 7. | Agreed approach |

The priority is compatibility with older Windows versions, rather than
preserving support for old hardware. If required Firefox functionality needs a
newer CPU instruction set, Foxy may raise its minimum CPU requirement.
Record the affected feature, reason, new minimum and validation before making
that change. An optional optimization alone does not justify dropping CPUs.

Memory, storage, driver and prerequisite-update requirements must come from
measured behavior and dependency requirements. Do not present ESR 115's
requirements as a verified guarantee for this newer codebase.

## Upstream version policy

Foxy follows upstream `release`, the stable Firefox track. Firefox 157.0.1 is the
initial compatibility milestone. Incorporate subsequent stable releases
deliberately, retaining a known baseline for each investigation and test result.

`foxy_dev` holds development, and validated work is promoted to `foxy_main`.
The upstream `main` development track and `foxy_next` are outside the current
active scope. ESR 115 provides historical compatibility references, not Foxy's
product baseline.

Record the exact upstream revision and Foxy changes for every tested and shipped
build. The first stable release's version is determined during release
preparation; completing the 157.0.1 milestone does not automatically make that
version the shipping release.

## Functionality and security

The first stable desktop release aims for full functionality of its corresponding
stable Firefox version, with behavior comparable to that version on modern
Windows. No feature is deliberately removed merely to make Windows 7 startup
easier.

Validation must cover browser and web-platform behavior, JavaScript and
WebAssembly, networking and TLS, graphics and WebGL, audio and video, WebRTC,
DRM, accessibility, extensions, developer tools, printing, downloads, profiles
and service integrations such as Sync. This is a coverage checklist, not an
exhaustive feature inventory.

Track features as working, untested or blocked against recorded environments.
Downloaded components, external services, codecs and drivers require their own
checks. A browser startup test does not establish their availability. A blocked
required feature prevents a full-parity claim unless the project explicitly
changes and records the release criteria.

Compatibility takes priority during initial development. Preserve upstream
security protections wherever feasible and aim for the strongest protections
available on each OS. Record every weakened protection or temporary workaround,
its affected systems and its validation. Review unresolved reductions before
release; do not imply identical security to modern Windows merely because
features work. Continue incorporating upstream fixes and testing for regressions.

## Firefox coexistence and distribution

Foxy must have independent application, installation and profile identities.
Its normal profile storage must be separate from Firefox's. Direct reuse of a
Firefox profile is undecided and must not be the default during development.

Identity work is an early milestone, covering update destinations and channels,
maintenance-service identity, registry entries, shortcuts, shell integration and
default-browser registration as well as branding and profile directories.
Foxy builds and tools must not accidentally update, remove or modify an existing
Firefox installation or profile.

Installation, upgrade, uninstall and recovery from failed or interrupted updates
are required functionality. Release preparation must establish Foxy's own
distribution and update configuration, signing decisions and tested recovery
behavior. Mozilla's submission infrastructure is not part of Foxy's routine CI
workflow; keep useful Mach build and test tooling and develop independent CI.

## Validation and release acceptance

Use both physical test machines and reproducible virtual-machine configurations,
with automated regression tests alongside repeatable manual checks. Contributors
may expand the test matrix as the project develops.

For each result, record:

- Foxy version, source revision, patch state, build configuration and toolchain.
- Windows edition, architecture, service pack and installed prerequisite updates.
- CPU model and capabilities, memory, GPU model and driver version.
- Test procedure, expected result, observed result and relevant logs or artifacts.
- Feature status and any known limitations or security workarounds.

Test the supported desktop Windows matrix, including modern Windows to detect
regressions introduced by compatibility work. Exercise hardware graphics and
media on physical machines; headless results do not replace those checks. Keep
automated tests for compatibility fixes and repeat them during upstream updates.

Before claiming a stable desktop release:

- Publish the tested OS, update, CPU and runtime prerequisites.
- Resolve required-feature blockers or explicitly revise the release criteria.
- Complete Firefox coexistence tests: install both, run both, update Foxy and
  uninstall Foxy while verifying Firefox's installation and profile remain intact.
- Validate installation, update and recovery behavior across the desktop matrix.
- Review security reductions and publish material limitations.
- Retain reproducible build instructions, test evidence and the source baseline.

Server validation follows the desktop release work. A planned Server milestone
is not a claim of existing Server support.

## Decisions still to establish

- Exact Windows 7 and other legacy Windows prerequisites.
- The tested hardware matrix and any justified increase in the CPU floor.
- Foxy's final product identifiers, profile paths and update/service configuration.
- External component and service availability on each target OS.
- Signing, release hosting, update delivery and contributor CI arrangements.
- Whether to offer controlled Firefox profile import or reuse in the future.

Update this specification when evidence resolves an open decision or the agreed
scope changes. Keep implementation progress in the [roadmap](roadmap.md).
