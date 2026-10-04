# Foxy

Foxy aims to provide current stable Firefox functionality on 64-bit Windows from
Windows 7 onward. These pages describe the agreed target, the evidence required
to claim support and the work needed to reach a stable release.

## Start here

- [Specification](specification.md): product scope and release requirements.
- [Roadmap](roadmap.md): milestones, dependencies and completion conditions.
- [Development guide](development.md): MozillaBuild setup, local validation and
  upstream maintenance.

Repository contribution instructions are in
[CONTRIBUTING.md](https://github.com/TheBinaryShadow/foxy/blob/foxy_dev/CONTRIBUTING.md).
The inherited Firefox documentation describes the engine and Mozilla tooling;
Foxy's contribution and submission workflow is defined in these project guides.

## Verified baseline and current limits

On October 4, 2026, a full optimized native x86_64 build of Firefox 157.0.1
completed on modern Windows. The source revision was
`e11529d218736fab0193a93783b649b1606ea428`, with a separate local Python build-monitor
repair for unavailable Windows performance counters. A temporary-profile
headless run rendered `about:buildconfig`.

That result establishes the initial build workflow. Windows 7 runtime support,
real GPU behavior and Foxy/Firefox coexistence remain unverified. Current builds
retain upstream unofficial Nightly branding and application identity. Use the
isolated testing guidance in the [development guide](development.md).

## Project documents

```{toctree}
:maxdepth: 2

specification
roadmap
development
```
