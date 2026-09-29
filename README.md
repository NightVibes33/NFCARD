# NFCARD

[![Build NFCARD IPA](https://github.com/NightVibes33/NFCARD/actions/workflows/build.yml/badge.svg)](https://github.com/NightVibes33/NFCARD/actions/workflows/build.yml)

NFCARD is a standalone iOS utility for pairing with the local device, discovering Apple Wallet cards, choosing custom artwork, and applying that artwork through the Airlift-based device-service path used by the project.

This repository contains the standalone NFCARD work migrated from the `temp/aircard-wallet-standalone` branch of `NightVibes33/Filza-27`. It no longer depends on the Filza-27 repository to build.

> **Status:** experimental. The build workflow produces an **unsigned IPA** for testing and research. NFCARD is not affiliated with or endorsed by Apple.

## Features

- Native dark graphite + mint NFCARD interface.
- Local iPhone pairing with PIN handling.
- Persistent paired state with remove/re-pair support.
- Live Wallet card discovery.
- Manual card entry when needed.
- Per-card and bulk artwork selection from Photos or Files.
- Card selection and artwork application.
- Embedded Card Library / Card Studio.
- Live Remote Pairing Bonjour port discovery instead of assuming only a fixed port.
- Prewarmed Library WebView to avoid the previous first-open hitch/jitter.
- Pairing plist filenames, raw pairing-file sizes, pairing logs, and network/IP diagnostics are hidden from the normal NFCARD UI.
- Upstream Passcode and Wallpaper features are intentionally not exposed.

## Current identity

- **Display name:** NFCARD
- **Bundle identifier:** `com.nightvibes33.aircard`
- **Upstream base:** `Mak5er/AirCard-iOS`
- **Pinned upstream commit:** `097a058c984ffc33ccb697b9dfe8058be3e86244`
- **NFCARD migration baseline:** `NightVibes33/Filza-27@fca787e1f565f715bdd5c7bc07a3118a2ee2e506`

The upstream revision is deliberately pinned so an upstream change cannot silently alter NFCARD builds.

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
- the Rust/iOS tooling used by the pinned AirCard `build-ios.sh`

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

The build fails closed if the pinned upstream source no longer matches the assumptions in the NFCARD patch set.

## GitHub Actions

Every push to `main`, pull request, or manual workflow dispatch runs **Build NFCARD IPA** on Xcode 26.2.

A green run uploads:

- `NFCARD-unsigned.ipa`
- `NFCARD-SHA256.txt`
- the full NFCARD build log

The patched AirliftFFI XCFramework is cached separately so normal UI-only changes do not force a Rust rebuild every run.

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

Normal users are not shown the raw pairing plist filename, file size, Remote Pairing port, or internal pairing log.

## Card Library

The Library tab hosts the NFCARD Card Studio at:

```text
https://cardmaker-omega.vercel.app
```

The WebView is kept mounted and preloaded so opening Library avoids the old root-view replacement and cold-WebKit hitch. The site's own navigation remains intact.

## Source model

This repository intentionally uses a **pinned upstream + deterministic patch** model rather than following upstream `main`.

That gives the standalone repository three properties:

1. NFCARD can be reproduced without depending on the Filza-27 repository.
2. Upstream AirCard remains clearly attributable and separately versioned.
3. Patch assertions fail the build if the expected upstream source shape changes.

## Attribution

NFCARD is based on **AirCard-iOS** by Johnny Franks and includes NFCARD-specific UI, standalone packaging, Card Library integration, and live Remote Pairing endpoint discovery.

The upstream project is MIT licensed. See [LICENSE](LICENSE) and [NOTICE.md](NOTICE.md).

## Disclaimer

NFCARD is experimental software intended for testing and research on devices you own or are authorized to test. Apple, Apple Wallet, iPhone, and related marks are trademarks of Apple Inc. This project is not affiliated with Apple.
