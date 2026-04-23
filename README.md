# Guile CI

Continuous integration workflows for [GNU Guile](https://www.gnu.org/software/guile/), running daily builds across a range of platforms and configurations.

## Build Status

| Platform | make | check | distcheck | dist verify |
|----------|------|-------|-----------|-------------|
| Ubuntu   | [![Ubuntu](https://github.com/spk121/guile-build/actions/workflows/ubuntu-make.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/ubuntu-make.yml) | [![Ubuntu](https://github.com/spk121/guile-build/actions/workflows/ubuntu.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/ubuntu.yml) | [![Ubuntu Distcheck](https://github.com/spk121/guile-build/actions/workflows/ubuntu-distcheck.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/ubuntu-distcheck.yml) | [![Ubuntu Dist Verify](https://github.com/spk121/guile-build/actions/workflows/ubuntu-dist-verify.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/ubuntu-dist-verify.yml) |
| Ubuntu ARM64    | | [![Ubuntu ARM64](https://github.com/spk121/guile-build/actions/workflows/ubuntu-arm.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/ubuntu-arm.yml) | | |
| Alpine          | [![Alpine Make](https://github.com/spk121/guile-build/actions/workflows/alpine-make.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/alpine-make.yml) | [![Alpine](https://github.com/spk121/guile-build/actions/workflows/alpine.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/alpine.yml) | | |
| MacOS           | | [![MacOS](https://github.com/spk121/guile-build/actions/workflows/macos.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/macos.yml) | | |
| Cygwin          | [![Cygwin Make](https://github.com/spk121/guile-build/actions/workflows/cygwin-make.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/cygwin-make.yml) | [![Cygwin](https://github.com/spk121/guile-build/actions/workflows/cygwin.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/cygwin.yml) | [![Cygwin Distcheck](https://github.com/spk121/guile-build/actions/workflows/cygwin-distcheck.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/cygwin-distcheck.yml) | |
| MSYS            | | [![MSYS](https://github.com/spk121/guile-build/actions/workflows/msys.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/msys.yml) | | |
| MinGW           | | [![MinGW](https://github.com/spk121/guile-build/actions/workflows/mingw.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/mingw.yml) | | |
| FreeBSD         | | [![FreeBSD](https://github.com/spk121/guile-build/actions/workflows/freebsd.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/freebsd.yml) | | |
| OpenBSD         | | [![OpenBSD](https://github.com/spk121/guile-build/actions/workflows/openbsd.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/openbsd.yml) | | |
| Debian GNU/Hurd | | [![Debian GNU/Hurd](https://github.com/spk121/guile-build/actions/workflows/hurd.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/hurd.yml) | | |

The columns correspond to build stages:

- **make** — `autogen.sh` → `configure` → `make` only; the test suite is not run.
- **check** — `autogen.sh` → `configure` → `make` → `make check`. The badge is green only when the test suite passes end-to-end.
- **distcheck** — `make distcheck`, which builds a distribution tarball and verifies it can be unpacked, configured, built, and tested in a clean tree.
- **dist verify** — runs `make distcheck`, then independently unpacks the produced tarball, configures and builds it from scratch, runs the test suite, and produces a fresh `make dist` tarball from the unpacked source. This catches issues where a tarball builds inside `distcheck`'s sandboxed environment but not in a normal user-style workflow.

## About

These workflows build and test [GNU Guile](https://www.gnu.org/software/guile/) on a variety of platforms, sourcing the code from [Codeberg](https://codeberg.org/guile/guile) by default. All workflows run daily on a schedule, and can also be triggered manually with a choice of source repository (Codeberg or GitHub) and a specific branch, tag, or commit.

### Platform coverage

The build matrix covers a deliberately wide range of environments:

- **Glibc Linux** on x86_64 (Ubuntu) and ARM64 (Ubuntu ARM64) — the mainline targets
- **Musl Linux** (Alpine) — exposes portability issues that glibc papers over, especially around locale and threading
- **MacOS** on Apple Silicon — Darwin / BSD userland with Homebrew
- **Windows POSIX layers** (Cygwin, MSYS) — full POSIX emulation on Windows
- **Native Windows** (MinGW matrix across MINGW64, MINGW32, UCRT64, CLANG64) — four different ABIs and toolchains
- **BSDs** (FreeBSD, OpenBSD) — non-Linux Unix variants
- **Hurd** — GNU's own kernel, surfacing assumptions that don't hold outside Linux

### Schedule

Daily builds are staggered through the day (UTC) to avoid all workflows starting at once:

- 07:00 to 08:00 — Ubuntu x86_64: make, check, distcheck, and dist-verify
- 08:00 to 09:00 — Cygwin: make, check, and distcheck
- 12:00 — MacOS
- 13:00 — MSYS
- 14:00 — MinGW
- 15:00 — FreeBSD
- 16:00 — Debian GNU/Hurd
- 17:00 — Ubuntu ARM64
- 18:00 to 19:00 — Alpine: make and check
- 19:00 — OpenBSD

### Manual runs

Every workflow exposes a **Run workflow** button under the Actions tab. Manual runs accept two inputs:

- **Repository** — choose between `https://codeberg.org/guile/guile.git` (default) and `https://github.com/spk121/guile.git`.
- **Ref** — an optional branch, tag, or commit SHA. Leave blank to use the default branch.

### Build artifacts

Each workflow uploads its logs as separate artifacts so a failure in one phase doesn't bury logs from earlier phases:

- `configure-log` — output of `autogen.sh` and `configure`, plus autotools' own `config.log`
- `build-log` — output of `make`
- `check-log` — output of `make check`, plus per-suite logs from `test-suite/tests/` and `test-suite/standalone/`

Distcheck and dist-verify workflows additionally produce:

- `distcheck-log` — output of `make distcheck`, including test logs from inside the distcheck build tree
- `dist-configure-log`, `dist-build-log`, `dist-check-log`, `dist-make-log` (dist-verify only) — logs from the independent rebuild of the distribution tarball
- `pkg-*-distcheck`, `pkg-*-dist-verify` — the produced distribution tarballs

Note that scheduled workflows on GitHub are automatically disabled if the repository sees no activity for around 60 days. If the badges all go grey at once, that's likely why — re-enable them from the Actions tab.

