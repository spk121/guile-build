# Guile CI

Continuous integration workflows for [GNU Guile](https://www.gnu.org/software/guile/), running daily builds across a range of platforms and configurations.

## Build Status

| Platform | make | check | distcheck | dist verify |
|----------|------|-------|-----------|-------------|
| Ubuntu   | [![Ubuntu](https://github.com/spk121/guile-build/actions/workflows/ubuntu-make.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/ubuntu-make.yml) | [![Ubuntu](https://github.com/spk121/guile-build/actions/workflows/ubuntu.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/ubuntu.yml) | [![Ubuntu Distcheck](https://github.com/spk121/guile-build/actions/workflows/ubuntu-distcheck.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/ubuntu-distcheck.yml) | [![Ubuntu Dist Verify](https://github.com/spk121/guile-build/actions/workflows/ubuntu-dist-verify.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/ubuntu-dist-verify.yml) |
| Ubuntu ARM64    | | [![Ubuntu ARM64](https://github.com/spk121/guile-build/actions/workflows/ubuntu-arm.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/ubuntu-arm.yml) | | |
| Alpine          | [![Alpine Make](https://github.com/spk121/guile-build/actions/workflows/alpine-make.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/alpine-make.yml) | [![Alpine](https://github.com/spk121/guile-build/actions/workflows/alpine.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/alpine.yml) | | |
| MacOS           | [![MacOS Make](https://github.com/spk121/guile-build/actions/workflows/macos-make.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/macos-make.yml) | [![MacOS](https://github.com/spk121/guile-build/actions/workflows/macos.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/macos.yml) | | |
| Cygwin          | [![Cygwin Make](https://github.com/spk121/guile-build/actions/workflows/cygwin-make.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/cygwin-make.yml) | [![Cygwin](https://github.com/spk121/guile-build/actions/workflows/cygwin.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/cygwin.yml) | [![Cygwin Distcheck](https://github.com/spk121/guile-build/actions/workflows/cygwin-distcheck.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/cygwin-distcheck.yml) | |
| MSYS            | [![MSYS Make](https://github.com/spk121/guile-build/actions/workflows/msys-make.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/msys-make.yml) | [![MSYS](https://github.com/spk121/guile-build/actions/workflows/msys.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/msys.yml) | | |
| MinGW           | [![MinGW Make](https://github.com/spk121/guile-build/actions/workflows/mingw-make.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/mingw-make.yml) | [![MinGW](https://github.com/spk121/guile-build/actions/workflows/mingw.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/mingw.yml) | | |
| FreeBSD         | [![FreeBSD Make](https://github.com/spk121/guile-build/actions/workflows/freebsd-make.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/freebsd-make.yml) | [![FreeBSD](https://github.com/spk121/guile-build/actions/workflows/freebsd.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/freebsd.yml) | | |
| OpenBSD         | [![OpenBSD Make](https://github.com/spk121/guile-build/actions/workflows/openbsd-make.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/openbsd-make.yml) | [![OpenBSD](https://github.com/spk121/guile-build/actions/workflows/openbsd.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/openbsd.yml) | | |
| Debian GNU/Hurd | [![Debian GNU/Hurd Make](https://github.com/spk121/guile-build/actions/workflows/hurd-make.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/hurd-make.yml) | [![Debian GNU/Hurd](https://github.com/spk121/guile-build/actions/workflows/hurd.yml/badge.svg)](https://github.com/spk121/guile-build/actions/workflows/hurd.yml) | | |

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
- 09:00 to 10:00 — MacOS: make and check
- 10:00 to 11:00 — MSYS: make and check
- 11:00 to 12:00 — MinGW 32-bit MSVCRT no-JIT: make and check
- 12:00 to 13:00 — MinGW 64-bit: make and check
- 13:00 to 14:00 — FreeBSD: make and check
- 14:00 to 15:00 — Debian GNU/Hurd: make and check
- 15:00 to 16:00 — Ubuntu ARM64: check
- 16:00 to 17:00 — Alpine: make and check
- 17:00 to 18:00 — OpenBSD: make and check

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

