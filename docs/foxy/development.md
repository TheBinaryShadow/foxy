# Developing Foxy

Foxy uses MozillaBuild and the existing Mach build and test tooling. The current
development track follows stable Firefox through `foxy_dev`; Windows 7
compatibility work does not require replacing the build system.

Read the [specification](specification.md) for the target and
[roadmap](roadmap.md) for milestone completion criteria. A native build that
runs on the build host is the starting point, not proof that it runs on every
target Windows version.

## Build host and runtime target

Use a modern x86_64 Windows 10 or Windows 11 machine as the build host, with
sufficient disk space and memory for a full Firefox build. The runtime target is
x86_64 desktop Windows 7 and newer. Windows 7 build hosts are not a current
requirement. Server installations with a desktop environment are a later
validation milestone; Server Core is excluded.

The initial CPU baseline is x86_64 with SSE2. Newer instruction sets may be used
after capability detection. Required features can justify raising the minimum
CPU requirement; any change needs a recorded decision and validation.

## Set up MozillaBuild

Install MozillaBuild using Mozilla's
[Windows build guidance](https://firefox-source-docs.mozilla.org/setup/windows_build.html).
Use the recommended `C:\mozilla-build` location to keep the setup consistent.
Keep the checkout in a short path without spaces, such as `C:\src\foxy`.

Open the MozillaBuild shell. The following commands use that shell, rather than
PowerShell or a separate Git Bash environment. Check that `git --version` works
in the shell before cloning; install Git for Windows if Git is unavailable.

```sh
mkdir -p /c/src
cd /c/src
git clone --branch foxy_dev https://github.com/TheBinaryShadow/foxy.git foxy
cd foxy
./mach bootstrap
```

Choose **Firefox for Desktop**, the full native build option, rather than
artifact mode. Compatibility changes require compiling the C++, Rust, and other
native code. Bootstrap installs the toolchains expected by the checked-out
source; do not substitute an older compiler merely because the runtime target
is Windows 7.

Enabling `sccache` is recommended for subsequent builds. Bootstrap's Git
configuration helpers are optional. Mozilla commit-submission setup is not
needed for Foxy. Build-system telemetry is a separate optional choice and does
not define browser telemetry behavior.

When bootstrap finishes, open a new MozillaBuild shell and return to the
checkout so that newly installed tools are available on `PATH`.

Bootstrap may create a local `mozconfig`. For the verified native baseline,
its only option was:

```sh
ac_add_options --with-ccache=sccache
```

Use this option only when `sccache` is installed. `mozconfig` is local build
configuration and is ignored by Git. Do not enable artifact mode for native
compatibility work. Record any additional options alongside test results.

## Build and run

From the checkout in the MozillaBuild shell:

```sh
mkdir -p artifacts
./mach build > artifacts/build.log 2>&1
```

Check the command's exit status and the saved log. Compilation may remain quiet
while large Rust crates compile or the main library links. Do not treat a lack
of recent log lines alone as a failed build.

The default x64 object directory is `obj-x86_64-pc-windows-msvc`, with the browser
at `dist/bin/firefox.exe` inside it. Use Mach to select the configured output
rather than copying it into an existing Firefox installation.

Foxy's independent profile, installer, update, service, and registration
identities have not yet been implemented. Until the coexistence milestone
passes, run local builds with a fresh temporary profile:

```sh
./mach run --temp-profile
```

Do not select an existing Firefox profile, make the test browser the default,
or install it over Firefox. A temporary profile keeps the test's profile data
separate; it does not establish complete isolation of application services,
registry integration, updates, or installer behavior. Test those activities only
in a disposable environment until Foxy's identities are separated and verified.

A headless startup and rendering check is useful before interactive testing:

```sh
./mach run --temp-profile -- --headless --screenshot about:buildconfig > artifacts/smoke.log 2>&1
```

Look for `Screenshot saved to:` in the log and inspect the generated
`screenshot.png` in the checkout. Move the image into `artifacts/` to retain it
with the log. The screenshot handler can report a failure while the process
still exits normally, so the exit status alone is not evidence of rendering.

This checks that the built browser can start and render a local page. It does
not validate hardware acceleration, physical GPU drivers, media support, or
Windows 7 compatibility. Those require the relevant target machines and feature
tests. Keep sandboxing and process isolation enabled when establishing the
baseline.

## Branches and upstream updates

| Branch or reference | Role |
| --- | --- |
| `foxy_dev` | Active Foxy development based on stable Firefox. |
| `foxy_main` | Project default branch; intended destination for reviewed and tested changes. |
| `upstream/release` | Remote reference for Mozilla's stable release source. |
| `upstream/main` | Mozilla's development source for future Firefox releases. |
| `foxy_next` | Existing development-track branch, outside the current stable-only work. |

`origin` points to the Foxy repository. If an upstream remote is not already
configured, add it once:

```sh
git remote add upstream https://github.com/mozilla-firefox/firefox.git
```

Fetch upstream changes for inspection:

```sh
git fetch upstream release
```

Fetching updates remote references without changing the working tree. Inspect
the new stable version and the Foxy patch set, then integrate it deliberately
on a branch based on `foxy_dev`. Preserve contributor work and published branch
history. Do not routinely rebase shared branches, reset to upstream, or pull
`origin/main` into the stable track.

For each integration, record the upstream version and revision, resolve
conflicts, run native and compatibility validation, and update the relevant
documentation. Promote validated work to `foxy_main` through review. The fixed
157.0.1 compatibility milestone remains a reference even as later stable
releases are incorporated. Independent release automation and CI are planned;
upstream infrastructure does not automatically provide Foxy CI.

## Local validation

Use the checks appropriate to the changed component:

```sh
./mach format
./mach lint
./mach test --auto > artifacts/tests.log 2>&1
```

Prefer focused tests while developing and `--headless` when the selected test
harness supports it. Use `./mach COMMAND --help` for harness-specific arguments.
Rebuild after native source changes; documentation-only changes do not require
another full browser build.

For Foxy documentation changes, use the applicable documentation linters and
build the documentation tree:

```sh
./mach lint -l codespell -l file-whitespace -l trojan-source README.md CONTRIBUTING.md AGENTS.md docs/foxy
./mach doc --no-serve --no-open > artifacts/docs.log 2>&1
```

The whole-tree documentation build also validates the generated navigation that
includes the Foxy pages. For faster rendering, `--no-autodoc` skips
Python/JavaScript API generation, but inherited pages can then warn about missing
API directives. Treat that as a preview rather than a complete validation pass.
Scoped Mach builds can also warn about omitted documentation trees because the
inherited global navigation still references them; check navigation changes with
the whole-tree build.

Redirect slow command output to a file in `artifacts/` and inspect that file as
needed. Do not pipe the running build or test command through filters that
discard output. Keep local logs and build products out of commits.

Mozilla Try and Phabricator submissions are not part of Foxy's default workflow.
Run local validation and report its scope until Foxy's own CI is available.

## Compatibility evidence

For each test machine, record the exact commit, upstream Firefox version,
build options, Windows edition and update level, CPU, GPU, driver version, test
steps, and results. Track features as working, failing, or untested. Retain logs
and a comparison result from the same machine when practical.

Use reproducible VM configurations for repeatable operating-system tests and
physical machines for behavior that depends on real hardware and drivers.
Automated regression tests should accompany compatibility fixes where feasible.
Windows 7 service-pack and update prerequisites will be established by testing;
ESR 115 is a comparison baseline, not a verified prerequisite list for Foxy.

## Verified native baseline

On 2026-10-04, a full optimized x64 native build of Firefox **157.0.1** at
`e11529d218736fab0193a93783b649b1606ea428` succeeded on a modern Windows host,
followed by a temporary-profile headless render of `about:buildconfig`.

| Tool | Version used |
| --- | --- |
| MozillaBuild | 4.2.1 |
| Clang | 22.1.8 |
| Rust | 1.99.0 |
| MSVC | 14.51.36231 |
| Windows SDK | 10.0.28000.0 |
| Compilation cache | sccache |

These versions describe that successful build, not a permanent toolchain pin or
a Windows 7 compatible runtime stack. The source selected unofficial Nightly
branding despite following the stable release source. Foxy branding, packaging,
updates, and Windows 7 validation remain roadmap work.

The build also included a local Python resource-monitor repair: when Windows
performance counters are unavailable, disabled monitoring must not attempt
streaming or profile export. Its two regression tests passed. This is a build
tooling issue rather than browser runtime compatibility. If a build fails, keep
the first relevant diagnostic and reproduce it before changing toolchains or
discarding the object directory.
