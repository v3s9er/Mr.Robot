# Mr.Robot 0.3.8 session handoff — 2026-09-01

## Resume in one sentence

Continue from commit `38c7deb`: Mr.Robot 0.3.8 is built, installed, and locally/publicly verified with hardened Cloudflare remote access, DPAPI-protected credentials, renderer secret isolation, scoped device capabilities, bounded model/file operations, and the 0.3.7 private-calendar behavior preserved.

## Completed in 0.3.8

- Public enrollment uses a separate cryptographically random, memory-only, one-use 12-digit handoff; the ordinary local PIN is rejected on the public endpoint.
- Public WebSockets require a short-lived, source/host/principal-bound one-use ticket. Origin, authentication, connection, message, byte, in-flight RPC, and heartbeat limits are enforced, and revocation closes live sessions.
- The Cloudflare Connector validates the exact Windows Authenticode publisher and runs from an app-generated local config containing only the validated public hostname to loopback Agent route plus a catch-all 404. Connector credentials are DPAPI-protected and never placed in process arguments or diagnostics.
- The administrator secret is stored in a separate DPAPI envelope and is not returned to the Electron renderer. Saved remote-PC bearer tokens remain in the Electron main process, which injects authorization only for an exact registered origin.
- MCP environment values are purpose-separated DPAPI ciphertext; legacy plaintext migrates before commands are exposed, and RPC responses reveal variable names only.
- Codex, Claude, dependency, voice, and Connector child processes use explicit environment allowlists. Unsafe CLI flags are filtered; native runs have hard time and output bounds.
- Dependency installers pin direct tool versions and voice-asset SHA-256 values, enforce TLS, verify downloads before use, and reject archive traversal, links, special files, excessive entries, and expansion bombs.
- Remote HTTP/WSS requires authenticated HTTPS/WSS except for loopback or a transport proven to use the actual Tailscale adapter. Public peer fetches pin validated public DNS answers while preserving TLS hostname validation.
- File/work transfers use narrow, memory-only, one-use grants plus capability, concurrency, rolling-byte, response-size, free-space, and shared-area limits.
- REST and WebSocket model starts share one admission policy for permissions, concurrency, frequency, and bounded token accounting; cancellation and failures release admission state.
- The encrypted private work calendar, bounded read-only XLSX import, Seoul holiday rules, strict NAVER routing, provider/model selection, reasoning controls, presets, mobile keyboard handling, QR flow, file transfer, work sync, MCP, voice, and CTF/Docker features remain available.

## Verification

- `npm run typecheck`, `npm run build`, and the complete functional/security suite passed. The UI contract was rerun successfully after the final wording correction.
- Leak stress passed with no detected leak and 1.46 MiB measured drift.
- Root and mobile production dependency audits report 0 vulnerabilities.
- Repository secret scan and diff check passed.
- Desktop version `0.3.8.0` is installed.
- Loopback and `robot.v3s9er.com` health requests returned 200. Unauthenticated status, foreign Origin, and unauthenticated public WebSocket attempts were rejected with 403, 403, and 401 respectively.
- The live Connector used the generated local ingress config and did not expose its credential in process arguments.
- Pairing credentials were rotated after installation; active device links are 0.
- GitHub `v0.3.8` is public with EXE, APK, checksum, and source assets whose server-side digests match the local files. The same four artifacts and the separate one-use handoff note were verified in the owner's private Google Drive.

## Verified artifacts

- Windows x64 installer: `release/Mr.Robot-Setup-0.3.8-x64.exe`
  - Size: 97,947,543 bytes
  - SHA-256: `EC90B76D9A91B721B9AC987BF67C24282AA2C4E14DD86ACF55877C4F68F41C1C`
  - Authenticode: not signed
- Android APK: `release/mobile/Mr.Robot-Mobile-0.3.8.apk`
  - Size: 87,543,948 bytes
  - SHA-256: `F86B434CC8E89D41E2B16ABA08C1F5178A064B6B75835C6AFB2811FAB3A2DFFF`
  - versionName `0.3.8`, versionCode `13`
  - Signer SHA-256: `EB782D956DABCA784D9E0AFC152BF7061ACE72CE805215E3C6502AAE72E1A0E6`
  - APK Signature Scheme v2: verified
- Source archive: `release/Mr.Robot-source-0.3.8.zip`
  - Size: 1,118,426 bytes
  - SHA-256: `16CFDE511ED3900C65DE3BD2CCEFEB6DB10DAAAA8E383C7109471C968DE8263B`

## Remaining security boundaries

- The Windows installer is not Authenticode-signed and may trigger SmartScreen. Verify its SHA-256 before installation.
- Direct installer dependencies and downloaded assets are pinned, but transitive npm/pip dependencies are not independently hash-locked.
- Native subscription CLIs are bounded by runtime, output, concurrency, and post-run accounting, but the provider does not expose an enforceable hard token ceiling for an individual run.
- A DeepSeek key was pasted into chat and must be revoked and reissued by the user in the provider console; repository cleanup cannot invalidate it.

## Sensitive-state rules

Never commit or publish provider keys, pairing credentials, handoff codes, Connector credentials, remote-PC bearer tokens, signing keys, DPAPI blobs, private workbook data, internal links, or workplace identifiers. Runtime secrets remain outside the repository and encrypted by the platform-specific secure store.

## Fast validation

```powershell
npm run typecheck
npm run build
npm test
npm run test:leak
npm audit --omit=dev
npm audit --omit=dev --prefix apps/mobile
```
