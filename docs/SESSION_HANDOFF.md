# Mr.Robot 0.3.2 session handoff — 2026-08-31

## Resume in one sentence

Continue from the security-audited 0.3.2 desktop/mobile ecosystem in `C:\Team\_Nameless\취미\BOT\Mr.Robot`; the source, Windows installer, Android APK, automated tests, remote-link hardening, leak soak, Android signing verification, checksums, and third-party notices are complete.

## Product state

Mr.Robot is a desktop-first agent workspace that also works from Android. The PC is fully usable without a paired phone. Each conversation can run a direct Codex, Claude Code, API, or local model, or apply a reusable multi-agent graph. Workspaces, model/reasoning choice, access policy, scenario, files, stop, and steering are conversation-scoped.

The 0.3.2 distribution supersedes older handoff entries and adds a fixed user-domain Cloudflare Tunnel without turning remote connectivity into a built-in dependency. Product-controlled source and documentation contain no legacy product-name references. Runtime data remains under `~/.mr-robot`; Orca, Tailscale and Remote Link are disabled by default.

## Completed desktop and agent behavior

- Polished responsive shell, navigation, conversation context menus, model/scenario/workspace/access selectors, drag-and-drop, file manager, calendar, plugin management, dependency setup, settings, loading/error/empty states, and explicit non-admin read-only affordances.
- Pin, rename, archive, restore, and delete conversations. Active work is tracked per conversation, so switching chats cannot strand or falsely stop another run.
- Direct single-model execution and reusable sequential, voting, validation, hybrid council, smart-cascade, and competitive CTF presets. Protected built-ins cannot be modified or deleted; copied/custom presets can.
- Compact graph nodes open focused editors. Edges, direction, groups, group bounds, debate rounds, voting, validation judges, fallbacks, create/edit/delete, and graph previews are persisted.
- ContextBroker fingerprints and bounds source material once, reuses shared summaries and provider cache prefixes, and evicts by byte-limited LRU. Usage separates input, output, reasoning, cache-read, and cache-write tokens.
- Per-run provider budgets, fallbacks, terminal-event validation, cancellation, safe-boundary steering, subprocess-tree cleanup, and Docker cleanup prevent silent hangs and post-completion spinning.
- Provider registration no longer locks the model. Discovered models, reasoning effort, default model, and per-conversation model remain switchable.
- Provider URLs, headers, and error text are sanitized; invalid authentication/404 health responses are rejected instead of being treated as a successful connection.

## Completed mobile behavior

- Android uses safe areas, `KeyboardAvoidingView`, `adjustResize`, and keyboard-aware bottom navigation so the composer and modal actions stay visible while typing.
- Conversation-isolated run state, stop, steering, reconnect/backoff, connection diagnostics, file progress, cancellation, and partial-file cleanup mirror the desktop workflow.
- Per-PC credentials are stored with SecureStore. The UI does not require a PC until the user chooses a remote workflow; desktop remains standalone.
- Mobile supports direct/single-model and complex preset execution through the selected PC, files, schedules, conversations, and device management.
- Mobile always-listening voice was intentionally removed. Desktop voice remains opt-in and configurable.

## Remote connection, transfer, and synchronization

- Plain HTTP LAN pairing is blocked. Use the loopback desktop app, a temporary HTTPS Quick Link, or an encrypted Tailscale address.
- The Remote Link plugin offers both an opt-in temporary Cloudflare Quick Link and a user-owned named Tunnel with a fixed HTTPS/WSS hostname. The latter accepts the hostname and Connector token directly in the plugin and can auto-connect at startup.
- Named-Tunnel credentials are write-only over RPC, DPAPI-protected at rest, passed through child environment rather than process arguments, redacted from diagnostics, and independently removable. The public endpoint is verified before its QR is shown.
- Tailscale is an optional plugin, not a built-in requirement. Keeping it disabled avoids conflicts with banking and other VPN-sensitive apps.
- File upload/download and work synchronization transfer bytes directly and do not invoke a model or consume model tokens.
- Cross-PC pulls use a 90-second, single-use capability. A source PC's long-lived device credential is never forwarded.
- Arbitrary public domains remain blocked for server-side PC-to-PC pull until peer identity can be cryptographically pinned; named Tunnel still supports ordinary mobile/desktop client chat and file access.
- Upload/download is cancellable, partial files are removed, path traversal is rejected, resolved paths stay inside allowed roots, and files are capped at 2 GiB.
- Work sync validates object count and byte limits before commit, merges by update time, and rolls back when persistence fails.

## Access and plugin security

- Global, paired-device, conversation, workspace, and destructive-action limits form hard ceilings and are rechecked at execution time.
- Remote non-admin sessions are read-only where management actions are unavailable; hiding a button is never the security boundary.
- Plugin calls receive host-owned execution contexts and AbortSignals. Plugins cannot forge another device's identity or broaden the current access grant.
- MCP tools remain modular. Calendar works locally and with ICS without cloud credentials. Google Calendar cloud access remains an optional user-owned OAuth integration.
- The CTF Docker runner confines resolved workspaces, rejects symlink/junction and time-of-check/time-of-use escapes, disables networking by default, applies process/capability/resource limits, and cleans up cancellation. Configured toolbox tag: `mr-robot/ctf-toolbox:0.3.2`.
- Public responses use CSP/no-store/clickjacking/capability-policy headers. Desktop downloads require a trusted main frame and a matching encrypted saved-PC origin+credential, reject redirects, and stop at 2 GiB.
- The installer dependency wizard detects missing allowlisted tools and can install/update them. Interactive Codex, Claude, Google, and other account logins are never bundled or copied.

## Final artifacts

- Windows installer: `C:\Team\_Nameless\취미\BOT\Mr.Robot\release\Mr.Robot-Setup-0.3.2-x64.exe`
  - Size: 97,783,858 bytes
  - SHA-256: `AFBDEC083B78E3C67507811AA4DA27D76F1D2585CB4225E8ED0C019BBB712932`
  - Authenticode: unsigned
- Android APK: `C:\Team\_Nameless\취미\BOT\Mr.Robot\release\mobile\Mr.Robot-Mobile-0.3.2.apk`
  - Size: 87,498,916 bytes
  - SHA-256: `29285CEB01BEB556FF9FAC32D66368782CE5F476992C4097F6ABE06503604707`
  - Package: `com.mrrobot.mobile`
  - Version: `0.3.2` / versionCode `7` / targetSdk `36`
  - APK Signature Scheme v2: verified
  - Signer certificate SHA-256: `EB782D956DABCA784D9E0AFC152BF7061ACE72CE805215E3C6502AAE72E1A0E6`
- Third-party notices: `THIRD_PARTY_NOTICES.txt`
  - 635 production packages, 973,444 bytes
  - SHA-256: `7971D237979B5EBD9B910A3A538530E73E14ABBD05942C9D8533CFB5E371D3F1`
  - Deterministically reproduced and embedded in the desktop package and APK

## Final verification

- `npm run typecheck`: passed for shared, agent, web, and mobile.
- `npm run build`: passed.
- `npm test`: passed smoke, core hardening, storage recovery, executable QR security, plugin execution security, provider security, scheduler, AI loop, dependencies, routing, CLI, v0.2 compatibility, 51-RPC UI contract, logger, and voice suites.
- `npm run test:leak`: passed. Total retained-heap drift was 1,332 KiB after 600 plugin cycles, 80 WebSocket cycles, and 20 stream start/stop cycles; no leak detected.
- Root full/production and mobile production `npm audit`: 0 vulnerabilities.
- License scan: passed; deterministic notices include Sharp/libvips LGPL components and all production package notices.
- The prior 0.3.1 responsive browser QA remains valid for unchanged surfaces; 0.3.2 additionally passed its focused Remote Link and desktop IPC UI contracts.
- Expo config and production export passed.
- Electron NSIS installer, Android release build, APK metadata, v2 signature, artifact hashes, embedded notices, and brand assets were independently verified.
- `git diff --check` passed; a repository-wide case-insensitive legacy-name scan returned no matches.

## Installation and upgrade notes

- Windows is not Authenticode-signed. SmartScreen may require **More info → Run anyway**; verify the SHA-256 first.
- Android 0.2.1 and older test APKs used the debug certificate. Android cannot update those in place with the dedicated 0.3 release key. Sync needed data, uninstall the old app once, install 0.3.2, and pair again. Android 0.3.0/0.3.1 can update directly because 0.3.2 uses the same signer and a higher versionCode.
- Future 0.3.x APKs can update normally only while the same release signing key is preserved. Its password is protected for this Windows user with DPAPI and is not stored in the repository.
- The Android release script refuses missing or partial signing state by default and verifies the official signer fingerprint after packaging. `-InitializeSigningKey` is reserved for intentionally creating a different signing identity.

## Honest external boundaries

- Quick Link URLs are temporary and change after restart. The 0.3.2 plugin supports a stable user-owned Cloudflare named Tunnel; the PC, Mr.Robot and cloudflared still need to be running.
- Windows public-trust signing needs a user-owned code-signing certificate.
- Windows React Native/NDK release builds require an ASCII-only and short checkout path. The 0.3.2 APK was built and verified from `C:\MR032` because the canonical project path contains Korean characters and long CMake object paths can exceed Win32 limits.
- A real physical-phone pass is still recommended for vendor-specific background restrictions, camera pairing, keyboard behavior, and remote transfers.
- Google Calendar cloud sync needs the user's OAuth client; local calendar and ICS work without it.
- If Orca accepted creation of a worktree before cancellation, Mr.Robot stops the process tree but does not delete that worktree automatically because it may contain user data.
- No system can guarantee universally best answers. The defaults optimize quality per token through routing, selective escalation, shared context, validation, and bounded parallelism.

## Sensitive-state rules

Never commit or log provider API keys, pairing secrets, PINs, device credentials, OAuth tokens, private signing keys, or DPAPI blobs. No such values are present in this release's source, documentation, or Git history. Runtime secrets stay in `~/.mr-robot` or the platform credential store.

## Fast validation commands

Run the source checks from `C:\Team\_Nameless\취미\BOT\Mr.Robot`:

```powershell
npm run typecheck
npm test
npm run test:leak
npm audit --omit=dev
npm run build:installer
```

Run the Android release command from a short ASCII-only checkout such as `C:\MR032`:

```powershell
powershell -ExecutionPolicy Bypass -File .\scripts\build-mobile-release.ps1 -OutputDirectory .\release\mobile
```
