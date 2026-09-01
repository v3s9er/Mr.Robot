# Mr.Robot 0.3.7 session handoff — 2026-09-01

## Resume in one sentence

Continue from the verified 0.3.7 Windows desktop release: upstream 0.3.6 model/reasoning, mobile keyboard, QR, and Quick Link behavior remain intact, while the new private work calendar is implemented, security-tested, installed, and loaded from DPAPI-protected derived state.

## Completed in 0.3.7

- Desktop and mobile display a real seven-column month calendar in `Asia/Seoul` while retaining ordinary calendar events and administrator schedules.
- The PC administrator can select a macro-free `.xlsx` and provide a one-time row/group identifier. Import opens one file descriptor with read-only access, bounds the ZIP/XML input, rejects macros and external links, never evaluates formulas, and retains neither the workbook path nor the identifiers.
- The workbook is never modified. Production-schema validation and encrypted import confirmed identical SHA-256, length, and last-write time on the local read-only source copy.
- Only derived date, work status, workplace label, manually supplied address, and override data enter the private store. State is Windows DPAPI(CurrentUser) ciphertext under `~/.mr-robot/private/work-calendar/` and is excluded from Git and ordinary PC sync.
- Sensitive replacement or deletion publishes the latest encrypted state as both primary and recovery copy, so removed NAVER credentials cannot return through corruption recovery.
- Mobile devices need the separate, revocable `private-calendar` capability. Read-only devices can view but cannot mutate; update events contain only a revision and date range and go only to administrators or authorized devices. Capability or authentication loss immediately clears rendered private data and invalidates late responses from the old connection.
- Weekends and Korean public holidays are evaluated as calendar dates in Seoul. The 2026 official table and later-year rules are effective-year-aware, so the new Labor Day and Constitution Day treatment does not change historical years.
- Manual per-date values take precedence and can be removed to restore the imported, weekend, or holiday value.
- NAVER access is opt-in. The fixed official API host receives addresses only for the live request; internal workplace labels become generic route names, and coordinates/distance/time are not cached or persisted.
- Desktop, web, and mobile accept only the exact generated `nmap://route/public|walk|car` contract. If Windows has no app-scheme handler, desktop converts only that validated route to an exact `https://map.naver.com` directions path with generic names. Unknown or duplicated query keys, credentials, ports, fragments, invalid coordinates, changed names, and wrong app identifiers are rejected.
- The PC UI can explicitly delete saved NAVER Client ID/Secret and consent. Device capability updates use an administrator-only atomic single-capability RPC to avoid stale-array permission races.
- Network responses are streamed with a 1 MiB ceiling, already-cancelled requests make no fetch, and raw transport errors cannot echo a private geocoding URL.
- Workbook filesystem failures cannot echo the private source path, invalid control characters are rejected, and unreferenced workplace addresses are pruned after reimport or override changes.

## Verification

- `npm run typecheck`: shared, agent, web, and mobile passed.
- `npm run build`: shared, agent, web production bundle passed.
- `npm test`: all smoke, hardening, recovery, QR, calendar, plugin/provider, scheduler, AI, compatibility, UI, mobile privacy, logger, and voice suites passed.
- `npm run test:leak`: passed after 600 plugin attach/detach cycles and WebSocket churn; no leak detected.
- Root and mobile production dependency audits report 0 vulnerabilities.
- Deterministic third-party notices contain 636 production packages, including the new exact `fflate@0.8.2` entry.
- The installed executable reports `0.3.7`, its health endpoint responds, and the live calendar plugin reports `0.3.7` with the encrypted work month configured.
- Final repository scans contain no private workbook, internal link, real person/team/company identifier, NAVER secret, signing key, or DPAPI state.

## Artifacts

- Windows x64 installer: `release/Mr.Robot-Setup-0.3.7-x64.exe`
  - Size: 97,909,785 bytes
  - SHA-256: `D9F2026D3F36A7EE96422B5110724A16FE760B20083F2C5C3991A4118964A042`
  - Authenticode: not signed
- Checksum file: `release/SHA256SUMS-0.3.7.txt`
- Android source: `0.3.7`, versionCode `12`, minSdk `24`, targetSdk `36`
- Android APK: not produced on this workstation because the preserved 0.3.x release signing key is unavailable. Do not initialize a different key for an update build.

## Sensitive-state rules

Never commit or log a workbook, internal Microsoft URL, row/group identifier, workplace/address value, provider key, pairing secret, NAVER credential, signing key, or DPAPI blob. Private runtime values stay outside the repository under the user's Windows profile. An internal Microsoft workbook must be downloaded by the already authenticated user and remains read-only to Mr.Robot.

## Fast validation

```powershell
npm run typecheck
npm run build
npm test
npm run test:leak
npm audit --omit=dev
npm audit --omit=dev --prefix apps/mobile
```

Build the Windows installer with `npm run build:installer`. Build Android only after restoring the existing official signing material, then run `scripts/build-mobile-release.ps1`; the script must verify the expected certificate before copying an APK into `release/mobile/`.
