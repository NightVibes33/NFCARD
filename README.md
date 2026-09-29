# NFCARD

[![Build NFCARD IPA](https://github.com/NightVibes33/NFCARD/actions/workflows/build.yml/badge.svg)](https://github.com/NightVibes33/NFCARD/actions/workflows/build.yml)

**NFCARD** is the standalone continuation of the Wallet-card artwork work that previously lived in the Filza-27 AirCard branch. This repository is now the canonical home for NFCARD.

NFCARD pairs with the local iPhone, discovers Apple Wallet cards, lets the user choose custom artwork, and applies that artwork through the project's Airlift-based device-service path.

> **Status:** experimental. The automated build produces an **unsigned IPA** for testing and research on devices you own or are authorized to test. NFCARD is not affiliated with or endorsed by Apple.

## Download

The rolling release is rebuilt from `main`:

**[Download the latest unsigned NFCARD IPA](https://github.com/NightVibes33/NFCARD/releases/download/NFCARD-latest/NFCARD-unsigned.ipa)**

Release page:

**[NFCARD Latest](https://github.com/NightVibes33/NFCARD/releases/tag/NFCARD-latest)**

Every successful push to `main` also keeps the GitHub Actions artifact and SHA-256 file.

## What moved from Filza-27

The latest standalone NFCARD implementation was migrated from:

- **Repository:** `NightVibes33/Filza-27`
- **Branch:** `temp/aircard-wallet-standalone`
- **Latest synchronized source commit:** `172101e48615341bebcdc62d599c5ee8530049ff`
- **Upstream AirCard base:** `Mak5er/AirCard-iOS@097a058c984ffc33ccb697b9dfe8058be3e86244`

The old Filza-27 standalone AirCard work is superseded by this repository. Future standalone NFCARD development should happen here rather than in Filza-27.

The upstream AirCard revision remains deliberately pinned so upstream changes cannot silently alter NFCARD builds.

## Features

- Native dark graphite + mint NFCARD interface.
- Local iPhone pairing with PIN handling.
- Persistent paired state with remove/re-pair support.
- Real LocalDevVPN readiness state rather than a generic VPN indicator.
- Live Remote Pairing Bonjour port discovery.
- Live Apple Wallet card discovery.
- Manual card entry when needed.
- Per-card and bulk artwork selection from Photos or Files.
- Card selection and artwork application.
- Embedded Card Library / Card Studio.
- Prewarmed Library WebView to avoid the previous first-open hitch/jitter.
- Pairing plist filenames, raw pairing-file sizes, pairing logs, and network/IP diagnostics are hidden from the normal NFCARD UI.
- Upstream Passcode and Wallpaper tabs are intentionally not exposed.

## Current identity

- **Display name:** `NFCARD`
- **Bundle identifier:** `com.nightvibes33.aircard`
- **Pinned upstream commit:** `097a058c984ffc33ccb697b9dfe8058be3e86244`
- **Filza-27 migration head:** `172101e48615341bebcdc62d599c5ee8530049ff`

The bundle identifier is retained for compatibility with the existing NFCARD/AirCard development state; the user-facing product name is NFCARD.

## Repository layout

```text
.
├── Sources/
│   ├── NFCARDNativeShell.swift
│   ├── AirCardLibrary.swift
│   └── RemotePairingPortDiscovery.swift
├── scripts/
│   ├── build.sh
│   ├── patch-upstream.py
│   └── generate-nfcard-icon.py
└── .github/workflows/
    └── build.yml
```

NFCARD keeps its custom UI and integration code in this repository. During a build, `scripts/build.sh` clones the pinned AirCard upstream revision, copies the NFCARD sources into it, applies the deterministic patch set, restores or rebuilds the patched AirliftFFI XCFramework, builds the app, and packages the unsigned IPA.

## Build locally

### Requirements

- macOS
- Xcode 26.2
- XcodeGen
- Git
- Python 3
- the Rust/iOS tooling required by the pinned AirCard `build-ios.sh`

Install XcodeGen if necessary:

```bash
brew install xcodegen
```

Build:

```bash
git clone https://github.com/NightVibes33/NFCARD.git
cd NFCARD
bash scripts/build.sh
```

Successful output:

```text
.build/NFCARD-unsigned.ipa
.build/NFCARD-SHA256.txt
```

The build is fail-closed: source assertions stop the build if the pinned upstream source no longer matches the NFCARD patch assumptions.

## GitHub Actions

Every push to `main`, pull request, or manual workflow dispatch runs **Build NFCARD IPA** on Xcode 26.2.

A green build validates and uploads:

- `NFCARD-unsigned.ipa`
- `NFCARD-SHA256.txt`
- the full NFCARD build log

For pushes to `main`, the workflow also updates the rolling `NFCARD-latest` GitHub release.

The patched AirliftFFI XCFramework is cached separately so normal UI-only changes do not force a Rust rebuild every run. If a restored cache is invalid, the standalone build falls back to rebuilding the XCFramework instead of accepting a broken cache.

## Pairing and Wallet flow

```text
Launch NFCARD
  ↓
Connect LocalDevVPN
  ↓
Pair This iPhone
  ↓
Approve the NFCARD pairing request / PIN
  ↓
Open Wallet and detect the target card
  ↓
Choose artwork
  ↓
Select the card
  ↓
Apply artwork
```

Normal users are not shown the raw pairing plist filename, pairing-file size, Remote Pairing port, or internal pairing logs.

## Card Library

The Library tab hosts the NFCARD Card Studio at:

```text
https://cardmaker-omega.vercel.app
```

The WebView remains mounted and preloaded so opening Library avoids the old root-view replacement and cold-WebKit hitch. The site's own navigation remains intact.

## Source model

NFCARD intentionally uses a **pinned upstream + deterministic patch** model rather than tracking upstream `main`.

That keeps the standalone project:

1. reproducible without depending on the Filza-27 repository at build time;
2. attributable to the upstream AirCard project;
3. protected by source-shape assertions that fail if expected upstream code changes.

## Attribution

NFCARD is based on **AirCard-iOS** by Johnny Franks and includes NFCARD-specific UI, standalone packaging, Card Library integration, LocalDevVPN state handling, and live Remote Pairing endpoint discovery.

The upstream project is MIT licensed. See [LICENSE](LICENSE) and [NOTICE.md](NOTICE.md).

## Disclaimer

NFCARD is experimental software intended for testing and research on devices you own or are authorized to test. Apple, Apple Wallet, iPhone, and related marks are trademarks of Apple Inc. This project is not affiliated with Apple.
