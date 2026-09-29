# NFCARD

[![Build NFCARD IPA](https://github.com/NightVibes33/NFCARD/actions/workflows/build.yml/badge.svg)](https://github.com/NightVibes33/NFCARD/actions/workflows/build.yml)

**NFCARD** is a standalone iOS app for discovering Apple Wallet cards and applying custom card artwork from Photos, Files, or the built-in Card Library.

> **Status:** experimental development build. NFCARD is intended for testing and research on devices you own or are authorized to test. It is not affiliated with or endorsed by Apple.

## Download

**[Download the latest unsigned NFCARD IPA](https://github.com/NightVibes33/NFCARD/releases/download/NFCARD-latest/NFCARD-unsigned.ipa)**

**[View the latest NFCARD release](https://github.com/NightVibes33/NFCARD/releases/tag/NFCARD-latest)**

The release includes:

- `NFCARD-unsigned.ipa`
- `NFCARD-SHA256.txt`

## Features

- Native dark graphite and mint NFCARD interface.
- Pair the current iPhone directly from NFCARD.
- PIN-based pairing flow with persistent paired state.
- Remove the active pairing and pair again when needed.
- LocalDevVPN readiness detection.
- Live Remote Pairing Bonjour port discovery.
- Apple Wallet card discovery.
- Manual card entry when automatic discovery is not suitable.
- Choose artwork per card from Photos or Files.
- Apply one artwork image to multiple cards.
- Select, deselect, replace, or clear card artwork.
- Apply artwork to selected Wallet cards.
- Embedded Card Library / Card Studio.
- Preloaded Library WebView for fast opening without the previous first-open hitch.
- User-facing pairing UI that hides raw pairing filenames, file sizes, IP addresses, ports, and internal logs.

## App flow

```text
Launch NFCARD
  ↓
Connect LocalDevVPN
  ↓
Pair This iPhone
  ↓
Approve the NFCARD pairing request and enter the PIN if requested
  ↓
Open Wallet and detect the target card
  ↓
Choose artwork
  ↓
Select the card
  ↓
Apply artwork
  ↓
Reopen Wallet if needed to refresh the card appearance
```

## Pairing

NFCARD presents pairing as a normal app flow instead of exposing its internal pairing record.

Before pairing, the app shows **Pair This iPhone**. During pairing, it shows the current pairing state and PIN when one is available. After pairing succeeds, NFCARD shows **Connected** and exposes a remove action so the pairing can be deleted and created again.

The normal interface does not expose the pairing plist filename, pairing-file byte count, Remote Pairing endpoint, raw network details, or internal pairing logs.

## Wallet Cards

The Wallet Cards tab is the main artwork workflow.

From there you can:

- start or stop Wallet card discovery;
- add a card manually;
- choose artwork from Photos or Files;
- assign artwork to one card or multiple cards;
- select or deselect cards;
- clear or replace artwork;
- apply artwork to the selected cards.

NFCARD keeps the underlying card identifiers and transport details out of the primary pairing interface while retaining the data required for the Wallet workflow.

## Card Library

The **Library** tab opens NFCARD Card Studio:

```text
https://cardmaker-omega.vercel.app
```

The Library WebView stays mounted and preloaded so entering the Library is immediate instead of paying the full WebKit startup cost when the tab is tapped.

The site's own Discover, Studio, Library, and Export navigation remains intact. NFCARD adds only the native integration required to return to the app and import supported artwork.

## Current app identity

- **Display name:** `NFCARD`
- **Bundle identifier:** `com.nightvibes33.aircard`
- **Pinned AirCard upstream revision:** `097a058c984ffc33ccb697b9dfe8058be3e86244`
- **Build output:** `.build/NFCARD-unsigned.ipa`

The bundle identifier is retained for compatibility with existing NFCARD development state. The user-facing product name is **NFCARD**.

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

NFCARD keeps its app-specific UI and integration code in this repository. The build script clones the pinned upstream revision, applies the NFCARD patch set, restores or rebuilds the patched AirliftFFI XCFramework, generates the NFCARD icon, builds the iOS app, validates the package, and produces the unsigned IPA.

## Build locally

### Requirements

- macOS
- Xcode 26.2
- XcodeGen
- Git
- Python 3
- Rust/iOS tooling required by the pinned AirCard build

Install XcodeGen if needed:

```bash
brew install xcodegen
```

Clone and build:

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

The build is fail-closed. NFCARD validates the expected source shape before producing an IPA so an unexpected upstream change does not silently alter the application.

## Build verification

The automated NFCARD build verifies:

- the pinned upstream revision;
- NFCARD Pairing, Wallet Cards, and Library integration;
- NFCARD branding;
- removal of the old Passcode and Wallpapers tabs from the active tab model;
- Remote Pairing endpoint support;
- Card Library integration;
- the generated app icon;
- the final bundle identifier and display name;
- IPA archive integrity;
- SHA-256 output.

A successful build publishes the unsigned IPA and checksum to the rolling **NFCARD Latest** release.

## Technical model

NFCARD uses a pinned upstream revision plus a deterministic patch layer.

The custom source in this repository owns the NFCARD interface, Card Library integration, Remote Pairing discovery, product identity, and packaging contract. The pinned dependency prevents upstream changes from silently changing the generated app between builds.

The patched AirliftFFI XCFramework is cached independently so ordinary UI changes do not require rebuilding the Rust layer every time.

## Attribution

NFCARD is based on **AirCard-iOS** by Johnny Franks and adds NFCARD-specific UI, standalone packaging, Card Library integration, LocalDevVPN state handling, and live Remote Pairing endpoint discovery.

The upstream project is MIT licensed. See [LICENSE](LICENSE) and [NOTICE.md](NOTICE.md).

## Disclaimer

NFCARD is experimental software intended for testing and research on devices you own or are authorized to test.

Apple, Apple Wallet, iPhone, and related marks are trademarks of Apple Inc. NFCARD is not affiliated with Apple.
