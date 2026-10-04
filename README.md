# Foxy

Foxy is a Firefox fork aiming to bring current stable Firefox functionality to
64-bit Windows versions from Windows 7 onward. It follows Mozilla's upstream
`release` branch, with Firefox 157.0.1 as the initial compatibility milestone.

## Current status

Foxy is in early development. A full native build of the 157.0.1 baseline has
completed on modern Windows, with a temporary-profile headless rendering check.
Windows 7 compatibility and independent Foxy application, installation and
profile identities have not been implemented or validated yet. Current builds
still use upstream unofficial Nightly branding.

Full Firefox feature parity is the goal for the first stable desktop release.
The provisional CPU baseline is x86_64 with SSE2; required features may justify
raising it. Windows Server installations with a desktop environment are a later
validation milestone. Server Core and 32-bit builds are outside the scope.

## Documentation

- [Foxy documentation](docs/foxy/index.md)
- [Specification](docs/foxy/specification.md)
- [Roadmap](docs/foxy/roadmap.md)
- [Development and build guide](docs/foxy/development.md)
- [Contributing](CONTRIBUTING.md)

MozillaBuild and Mach remain the build environment and command interface.
Foxy's guide documents the tested setup and distinguishes the modern Windows
build host from the intended older Windows runtime targets.

## Branches and upstream

- `foxy_dev`: active development and compatibility work.
- `foxy_main`: project default branch, intended for validated changes and releases.
- `foxy_next`: reserved for future upstream-main investigation; outside the
  current stable-release scope.

The upstream Firefox repository is
[mozilla-firefox/firefox](https://github.com/mozilla-firefox/firefox).
Its `release` branch supplies our stable baseline; its `main` branch contains
development toward future Firefox releases.

## Contributing and reporting issues

Read the [contribution guide](CONTRIBUTING.md) before testing or changing Foxy.
Until the identity and coexistence milestone is complete, use an isolated test
environment and temporary profiles. Preserve existing Firefox installations and
profiles; a temporary profile alone does not isolate installation or shell
integration.

Report problems through
[Foxy Issues](https://github.com/TheBinaryShadow/foxy/issues), including the build
revision and machine details described in the contribution guide. Inherited
Mozilla PR automation still needs adaptation before the public pull-request
workflow can be relied on; this is tracked in the roadmap.

## Upstream reference and licensing

[Firefox Source Docs](https://firefox-source-docs.mozilla.org/) remains a useful
reference for the inherited engine and tooling. Mozilla contribution and CI
instructions there describe Mozilla's workflow; use Foxy's contribution guide
for this project.

Foxy retains the inherited source licenses and notices. See [LICENSE](LICENSE)
and the notices in individual files.
