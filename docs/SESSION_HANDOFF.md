# Mr.Robot 0.3.6 session handoff — 2026-08-31

## Resume in one sentence

Continue from the verified 0.3.6 desktop/mobile ecosystem in `C:\Team\_Nameless\취미\BOT\Mr.Robot`; provider-aware composer reasoning controls, atomic execution-setting saves, the 0.3.5 keyboard and Quick Link recovery fixes, Windows installer, official-signer Android APK, automated tests, leak soak, and third-party notices are complete. Create/publish the `v0.3.6` release from the prepared artifacts.

## Product state

Mr.Robot is a desktop-first agent workspace that also works from Android. The PC is fully usable without a paired phone. Each conversation can run a direct Codex, Claude Code, API, or local model, or apply a reusable multi-agent graph. Workspaces, model/reasoning choice, access policy, scenario, files, stop, and steering are conversation-scoped.

The 0.3.6 distribution supersedes older handoff entries. It keeps the fixed user-domain Cloudflare Tunnel, installs its small connector dependency when missing, and never enables or exposes Remote Link without an explicit administrator action. A separate 24-hour handoff credential is created only on an administrator request and is never persisted. Product-controlled source and documentation contain no legacy product-name references. Runtime data remains under `~/.mr-robot`; Orca, Tailscale and Remote Link are disabled by default.

## Completed desktop and agent behavior

- Polished responsive shell, navigation, conversation context menus, model/scenario/workspace/access selectors, drag-and-drop, file manager, calendar, plugin management, dependency setup, settings, loading/error/empty states, and explicit non-admin read-only affordances.
- Pin, rename, archive, restore, and delete conversations. Active work is tracked per conversation, so switching chats cannot strand or falsely stop another run.
- Direct single-model execution and reusable sequential, voting, validation, hybrid council, smart-cascade, and competitive CTF presets. Protected built-ins cannot be modified or deleted; copied/custom presets can.
- Compact graph nodes open focused editors. Edges, direction, groups, group bounds, debate rounds, voting, validation judges, fallbacks, create/edit/delete, and graph previews are persisted.
- ContextBroker fingerprints and bounds source material once, reuses shared summaries and provider cache prefixes, and evicts by byte-limited LRU. Usage separates input, output, reasoning, cache-read, and cache-write tokens.
- Per-run provider budgets, fallbacks, terminal-event validation, cancellation, safe-boundary steering, subprocess-tree cleanup, and Docker cleanup prevent silent hangs and post-completion spinning.
- Provider registration no longer locks the model. Discovered models, reasoning effort, default model, and per-conversation model remain switchable.
- Desktop and Android expose provider-aware reasoning controls at the composer bottom. Model, preset, reasoning, workspace, and access changes acquire a synchronous persistence lock so Send and voice execution cannot start with the previous context.
- Failed conversation updates restore all in-memory fields and sync metadata; desktop uses matching-field optimistic rollback and Android never restores a stale whole-conversation object.
- Provider URLs, headers, and error text are sanitized; invalid authentication/404 health responses are rejected instead of being treated as a successful connection.
- The plugin view no longer resubscribes and refreshes through a `remoteStatus → plugins → remoteStatus` cycle. Orca and Cloudflare buttons stay visually stable, and passive refresh no longer spawns repeated CLI/dependency probes.
- Stopped Remote Link status refresh preserves the saved fixed-Tunnel `autoStart` preference.
- `remote-link.changed` is an administrator-only forwarded event. The plugin and connection settings now update the live public address, reachability, configuration, and pairing QR immediately, and the running address remains visible and copyable even while the Remote Link card is collapsed.
- The profile menu's **원격 PC 관리** action now enters a durable management state. Automatic last/local-PC reconnection is suppressed only while that explicit screen is open, removing the previous open-close flicker without changing ordinary startup behavior.

## Completed mobile behavior

- Android uses safe areas, `KeyboardAvoidingView`, `adjustResize`, and keyboard-aware bottom navigation so the composer and modal actions stay visible while typing.
- Conversation-isolated run state, stop, steering, reconnect/backoff, connection diagnostics, file progress, cancellation, and partial-file cleanup mirror the desktop workflow.
- Per-PC credentials are stored with SecureStore. The UI does not require a PC until the user chooses a remote workflow; desktop remains standalone.
- Mobile supports direct/single-model and complex preset execution through the selected PC, files, schedules, conversations, and device management.
- Mobile always-listening voice was intentionally removed. Desktop voice remains opt-in and configurable.
- Mobile QR pairing is now two-stage: unrelated QR codes do not stop scanning; a valid Mr.Robot payload freezes into a review card that shows every candidate route; only **인식한 PC에 연결** exchanges the PIN. **다시 스캔** or closing the modal clears the candidate without connecting.
- Mobile manual entry and QR parsing accept only the ordinary 6-digit PIN or the administrator-created 12-digit handoff code. Desktop pairing QR uses a 300-pixel image, four-module quiet zone, and error-correction level M.

## Remote connection, transfer, and synchronization

- Plain HTTP LAN pairing is blocked. Use the loopback desktop app, a temporary HTTPS Quick Link, or an encrypted Tailscale address.
- The Remote Link plugin offers both an opt-in temporary Cloudflare Quick Link and a user-owned named Tunnel with a fixed HTTPS/WSS hostname. The latter accepts the hostname and Connector token directly in the plugin and can auto-connect at startup.
- Named-Tunnel credentials are write-only over RPC, DPAPI-protected at rest, passed through child environment rather than process arguments, redacted from diagnostics, and independently removable. The public endpoint is verified before its QR is shown.
- A local desktop administrator can create a separate 12-digit, one-time remote handoff code valid for up to 24 hours while a public link is running. It exists only in agent memory and is never written to configuration or logs.
- Successful use of either the 6-digit PIN or 12-digit handoff code consumes both enrollment paths. PIN regeneration, explicit handoff revocation, Remote Link stop, and agent stop/restart also revoke the handoff. Enrollment grants at most non-admin `ask` access until the local PC administrator explicitly raises the device ceiling.
- Tailscale is an optional plugin, not a built-in requirement. Keeping it disabled avoids conflicts with banking and other VPN-sensitive apps.
- File upload/download and work synchronization transfer bytes directly and do not invoke a model or consume model tokens.
- Cross-PC pulls use a 90-second, single-use capability. A source PC's long-lived device credential is never forwarded.
- Arbitrary public domains remain blocked for server-side PC-to-PC pull until peer identity can be cryptographically pinned; named Tunnel still supports ordinary mobile/desktop client chat and file access.
- Upload/download is cancellable, partial files are removed, path traversal is rejected, resolved paths stay inside allowed roots, and files are capped at 2 GiB.
- Work sync validates object count and byte limits before commit, merges by update time, and rolls back when persistence fails.

## Access and plugin security

- Global, paired-device, conversation, workspace, and destructive-action limits form hard ceilings and are rechecked at execution time.
- Remote non-admin sessions are read-only where management actions are unavailable; hiding a button is never the security boundary.
- Paired non-administrator sessions receive no PIN, QR payload, local secret, or administrator-secret fingerprint (`maskedSecret`). Remote handoff creation and revocation are administrator-only RPCs.
- Plugin calls receive host-owned execution contexts and AbortSignals. Plugins cannot forge another device's identity or broaden the current access grant.
- MCP tools remain modular. Calendar works locally and with ICS without cloud credentials. Google Calendar cloud access remains an optional user-owned OAuth integration.
- The CTF Docker runner confines resolved workspaces, rejects symlink/junction and time-of-check/time-of-use escapes, disables networking by default, applies process/capability/resource limits, and cleans up cancellation. Configured toolbox tag: `mr-robot/ctf-toolbox:0.3.4`.
- Public responses use CSP/no-store/clickjacking/capability-policy headers. Desktop downloads require a trusted main frame and a matching encrypted saved-PC origin+credential, reject redirects, and stop at 2 GiB.
- Dependency wizard v5 detects missing allowlisted tools and automatically includes cloudflared. Its probe covers WinGet Links, Program Files, and Program Files (x86); installation uses the official x64 user-scoped portable WinGet package. The plugin also exposes an explicit install button, with long RPC timeouts for real package-manager latency. Interactive Codex, Claude, Google, and other account logins are never bundled or copied.

## Final artifacts

- Windows installer: `C:\Team\_Nameless\취미\BOT\Mr.Robot\release\Mr.Robot-Setup-0.3.6-x64.exe`
  - Size: 97,788,841 bytes
  - SHA-256: `5C309FA2B2D83D3A5BD1639EA9DDF049050CE4D34BB4B91B55CD4EEC63C1BD3A`
  - Authenticode: unsigned
- Android APK: `C:\Team\_Nameless\취미\BOT\Mr.Robot\release\mobile\Mr.Robot-Mobile-0.3.6.apk`
  - Size: 87,513,720 bytes
  - SHA-256: `950230B6D77E156E66009B4973F343B71663761DC3DAA9556B7F6D761FF389B6`
  - Package: `com.mrrobot.mobile`
  - Version: `0.3.6` / versionCode `11` / minSdk `24` / targetSdk `36`
  - Permission: `android.permission.CAMERA`
  - APK Signature Scheme v2: verified
  - Signer certificate SHA-256: `EB782D956DABCA784D9E0AFC152BF7061ACE72CE805215E3C6502AAE72E1A0E6`
- Third-party notices: `THIRD_PARTY_NOTICES.txt`
  - 635 production packages, 973,444 bytes
  - SHA-256: `7971D237979B5EBD9B910A3A538530E73E14ABBD05942C9D8533CFB5E371D3F1`
  - Deterministically reproduced and embedded in the desktop package and APK

## Final verification

- `npm run typecheck`: passed for shared, agent, web, and mobile.
- `npm run build`: passed.
- `npm test`: passed smoke, core hardening, storage recovery, executable QR security, plugin execution security, provider security, scheduler, AI loop, dependencies, routing, CLI, v0.2 compatibility, UI contract, logger, and voice suites. New coverage includes execution-setting locks and complete rollback after an injected persistence failure.
- `npm run test:leak`: passed; no leak detected, about 1.3 MB total measured heap drift.
- Root full/production and mobile production `npm audit`: 0 vulnerabilities.
- License scan: passed; deterministic notices include Sharp/libvips LGPL components and all production package notices.
- The prior responsive browser QA remains valid for unchanged surfaces; 0.3.6 additionally passed composer reasoning, execution-setting lock, mobile keyboard/modal avoidance, Quick Link recovery, and desktop IPC UI contracts.
- Expo config and production export passed.
- Electron NSIS installer, Android release build, APK metadata, v2 signature, artifact hashes, embedded notices, and brand assets were independently verified.
- Temporary Android staging `C:\MR036` was removed after artifact verification.
- `git diff --check` passed; a repository-wide case-insensitive legacy-name scan returned no matches.

## Installation and upgrade notes

- Windows is not Authenticode-signed. SmartScreen may require **More info → Run anyway**; verify the SHA-256 first.
- Android 0.2.1 and older test APKs used the debug certificate. Android cannot update those in place with the dedicated 0.3 release key. Sync needed data, uninstall the old app once, install 0.3.6, and pair again. Android 0.3.0 through 0.3.4 can update directly because 0.3.6 uses the same signer and a higher versionCode.
- Future 0.3.x APKs can update normally only while the same release signing key is preserved. Its password is protected for this Windows user with DPAPI and is not stored in the repository.
- The Android release script refuses missing or partial signing state by default and verifies the official signer fingerprint after packaging. `-InitializeSigningKey` is reserved for intentionally creating a different signing identity.

## Honest external boundaries

- Quick Link URLs are temporary and change after restart. The 0.3.6 plugin supports a stable user-owned Cloudflare named Tunnel; the PC, Mr.Robot and cloudflared still need to be running. The 24-hour handoff code does not keep a stopped tunnel or PC alive.
- Windows public-trust signing needs a user-owned code-signing certificate.
- Windows React Native/NDK release builds require an ASCII-only and short checkout path. The 0.3.6 APK was built and verified from temporary staging `C:\MR036` because the canonical project path contains Korean characters and long CMake object paths can exceed Win32 limits; that staging directory has been removed.
- A real physical-phone pass is still recommended for vendor-specific background restrictions, camera pairing, keyboard behavior, and remote transfers.
- Google Calendar cloud sync needs the user's OAuth client; local calendar and ICS work without it.
- If Orca accepted creation of a worktree before cancellation, Mr.Robot stops the process tree but does not delete that worktree automatically because it may contain user data.
- No system can guarantee universally best answers. The defaults optimize quality per token through routing, selective escalation, shared context, validation, and bounded parallelism.

## Sensitive-state rules

Never commit or log provider API keys, pairing secrets, ordinary PINs, remote handoff codes, device credentials, OAuth tokens, private signing keys, or DPAPI blobs. No such values are present in this release's source or documentation. Runtime secrets stay in `~/.mr-robot`, agent memory, or the platform credential store.

## Fast validation commands

Run the source checks from `C:\Team\_Nameless\취미\BOT\Mr.Robot`:

```powershell
npm run typecheck
npm test
npm run test:leak
npm audit --omit=dev
npm run build:installer
```

Run the Android release command from a temporary short ASCII-only checkout such as `C:\MR034`, then remove that staging directory after verified artifacts are copied back:

```powershell
powershell -ExecutionPolicy Bypass -File .\scripts\build-mobile-release.ps1 -OutputDirectory .\release\mobile
```
