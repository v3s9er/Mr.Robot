# Mr.Robot session handoff — 2026-08-20

## Resume in one sentence

Continue developing `C:\Team\_Nameless\취미\BOT\Mr.Robot` as an installable Windows/mobile AI-agent hub; the newest completed work is grouped model councils with validation judging, three efficient workflow presets, and token-free direct device file/work synchronization.

## Completed architecture and UI

- Persistent multi-conversation storage, chat streaming, context compression, structured memory, and reasoning-effort selection.
- Multi-provider routing for API, local, Codex subscription CLI, and Claude subscription CLI models.
- Device pairing, device-specific permission caps, admin-only management, and PC delegation.
- Freely arranged routing graph with model, critic, memory, executor, and Orca execution nodes.
- Four protected built-in routing presets plus persistent user presets with save/apply/overwrite/delete RPC and UI flows.
- Provider models remain editable after registration; discovered model lists feed the same editor.
- Free/local sources are always cost tier 0, strict health checks reject authentication/404 failures, and Ollama `/v1` URLs normalize correctly.
- Orca delegation and automatic launch default to disabled; an explicit saved opt-in remains persistent.
- Codex-like sidebar/profile/settings navigation and selective tool exposure.
- Built-in Orca execution backend and Windows NSIS installer.
- Every graph node can be assigned a role, provider/model, and free-form group. Vote/hybrid groups exchange opinions for 1–3 rounds; the final critic node independently validates the result rather than blindly following the majority.
- Added built-ins: low-cost efficient vote, sequential execution/validation, and classification/council/validation hybrid. The preset browser is a clickable list with a read-only graph preview.
- Authenticated direct file streaming is restricted to `~/.mr-robot/shared`; PC-to-PC pulls do not route bytes through the phone or an AI model.
- Versioned work sync merges conversations and user presets by latest update time. Mobile and desktop both expose the device mesh UI.
- Mobile chat selects PC-default commands, one explicit model, or any saved complex-tree preset per conversation.

## Rename completed

- Product/display name: `Mr.Robot`.
- npm/workspace and protocol identifier: `mr-robot` / `@mr-robot/*`.
- code identifiers: `MrRobot*`; environment variables: `MR_ROBOT_*`.
- runtime directory: `~/.mr-robot`; installer: `Mr.Robot-Setup-0.1.0-x64.exe`.
- Product-controlled source, docs, generated web/agent output, and packaged app contain no old product-name references.
- Third-party `node_modules` still contains Google device-emulation image names from React Native DevTools; generated dependency assets must not be renamed.

## First-run dependency setup completed

- First authenticated desktop connection opens a modal until the check is marked complete.
- The same screen remains available under Settings → External tools.
- Detects actual executability and known install paths for Node.js LTS, Git, Codex CLI, Claude Code, Orca, Ollama, and Tailscale.
- Uses a fixed allowlist only: `winget` IDs for Node/Git/Orca/Ollama and official npm packages for Codex/Claude.
- Commands are spawned without a user-controlled shell string; output and time are bounded, and concurrent installs are blocked.
- Codex/Claude authentication stays interactive and is never bundled or copied.

## Important changed files

- `packages/agent/src/dependencies.ts`
- `packages/agent/src/config.ts`
- `packages/agent/src/server/server.ts`
- `packages/agent/src/ai/provider.ts`, `registry.ts`, `openai.ts`, `anthropic.ts`, `cli.ts`
- `packages/agent/src/plugins/orca.ts`
- `packages/shared/src/protocol.ts`
- `packages/web/src/components/DependencySetup.tsx`
- `packages/web/src/App.tsx`
- `packages/web/src/views/SettingsView.tsx`
- `packages/web/src/styles.css`
- `packages/agent/test/dependencies.mjs`
- `packages/agent/test/routing-presets.mjs`
- `scripts/stage-desktop.mjs`
- all product source/config/docs touched by the rename

## Local environment and artifacts

- Project: `C:\Team\_Nameless\취미\BOT\Mr.Robot`.
- Installer: `C:\Team\_Nameless\취미\BOT\Mr.Robot\release\Mr.Robot-Setup-0.1.0-x64.exe`.
- Android APK: `C:\Team\_Nameless\취미\BOT\Mr.Robot\release\mobile\Mr.Robot-Mobile-0.1.1.apk` (release bundle, locally signed for sideloading).
- Installer is built but not installed on this Windows account.
- Desktop/web dependencies: React 19.2.8, Express 5.2.1, Zod 4.4.3, TypeScript 7.0.2; root audit has zero vulnerabilities.
- Mobile compatibility set: Expo 57.0.15, React 19.2.3, React Native 0.86.2, TypeScript 6.0.3; Expo Doctor passes 21/21 checks.
- Installed external tools: Node.js 24.13.1, Git 2.53.0, Codex CLI 0.148.0, Claude Code 2.1.237, Orca 1.4.185, Ollama 0.32.9, Tailscale 1.102.2.
- Tailscale is installed but currently reports `NeedsLogin`; the user must complete the interactive Google login on this PC and the phone.
- Orca CLI: `C:\Users\lrsze\AppData\Local\Programs\orca\resources\bin\orca.exe`; installed but runtime currently stopped.
- Claude Code still requires interactive account login. Codex credentials were not copied or modified.

## Persistent state and security

- New live state will be created at `C:\Users\lrsze\.mr-robot` on first application launch.
- The archived state snapshot is stored in the project’s `.mr-robot` directory.
- DPAPI ciphertext is bound to the Windows user that created it and is not portable between accounts.
- Never print API keys, pairing secrets, PINs, or third-party login tokens in logs.
- Plaintext credentials supplied in the handoff message were not written into source, docs, or logs.

## Verification snapshot

- root `npm run typecheck`: passed.
- root `npm run build`: passed.
- root `npm test`: passed smoke, scheduler, AI-loop, dependency-check, routing-preset, provider-health, and model-switch tests.
- mobile `npm run typecheck`: passed.
- Browser QA: first-run dependencies, routing preset apply/save/list, free local provider card, post-registration model change, and Orca-off defaults passed.
- `npm run build:installer`: passed after making desktop staging robust for non-ASCII Windows paths.
- Installer is unsigned; code signing remains required for public distribution.
- Mobile npm audit still reports Expo/Metro development-tool transitive advisories; npm's only forced proposal downgrades Expo to SDK 53, so it was rejected while Expo Doctor remains clean.
- Direct file upload/download, PC-to-PC pull, path-traversal blocking, snapshot export, and zero-AI-token work pull passed automated HTTP tests.
- Browser QA passed for the device share screen and clickable preset list/graph preview, including the grouped low-cost voting preset.
- Mobile 0.1.1 keeps the camera open until a QR connection actually succeeds, shows scan errors in place, and falls back from saved LAN endpoints to a Tailscale endpoint automatically.
- Pairing QR payload v3 stores LAN plus authenticated Tailscale addresses. Link-local adapter addresses are rejected; only Tailscale's `100.64.0.0/10` IPv4 range is accepted.
- Browser QA passed for the mobile connection panel: it shows `192.168.0.79:8787` for LAN and correctly reports that no external address exists before Tailscale login.
- The current unpacked desktop app was restarted and `/api/ping` is healthy on port 8787.
- APK SHA-256: `919AC56A0A5EF34895307AF1FE610C1408EC58EBE6B96F174122BE81C0196A84`; installer SHA-256: `C38B2A457C853BB8B98DB79418F675F1E5026F1A2305CDF8876578962F521A04`.

## Recommended next implementation order

1. Run the new installer and complete a clean-user first-launch test.
2. Complete Claude interactive login, register this Git repository in Orca, and run Codex/Claude delegation E2E.
3. Install the APK on the physical phone, pair both laptops over LAN, then test optional Tailscale connectivity away from home.
4. Track the upstream Expo/Metro security fixes without downgrading SDK 57.
5. Rework leak-test baseline with a warm-up phase.
6. Add application icon, code signing, version bumping, and auto-update.

## Fast validation commands

Run from `C:\Team\_Nameless\취미\BOT\Mr.Robot`:

```powershell
npm run typecheck
npm test
npm run build:installer
npm run typecheck --prefix apps/mobile
& 'C:\Users\lrsze\AppData\Local\Programs\orca\resources\bin\orca.exe' status --json
```

## 0.2.0 final handoff (2026-08-23)

The 0.2.0 implementation supersedes the older 0.1.x snapshot above. The current desktop process is the freshly built unpacked 0.2.0 app and its health endpoint passes at `http://127.0.0.1:8787/api/ping`.

### Final artifacts

- Windows installer: `release/Mr.Robot-Setup-0.2.0-x64.exe`
- Android package: `release/mobile/Mr.Robot-Mobile-0.2.0.apk`
- CTF sandbox image: `mr-robot/ctf-toolbox:0.2.0`
- Machine-readable resume cache: `docs/SESSION_STATE.json`
- User-facing guide: `docs/USER_GUIDE_0.2.md`
- Research and attribution notes: `docs/RESEARCH_AND_LICENSES.md`

The Android build was produced from `C:\Users\lrsze\MrRobotMobileBuild`, an ASCII-only mirror of the same source. This is a Windows NDK/Ninja path workaround for the Korean characters in the canonical project path; the canonical project remains `C:\Team\_Nameless\취미\BOT\Mr.Robot`.

### Completed product behavior

- A conversation can select no scenario for a direct single-model Codex/Claude run, or select sequential, vote, validation, hybrid, smart-routing, or CTF scenarios.
- Scenario nodes are compact role cards. Opening a node exposes role, provider/model, group, prompts, edges, debate rounds, voting, validation, and fallback behavior.
- The shared context broker reads and fingerprints source material once, then distributes bounded references and cached summaries to participating models instead of repeatedly uploading the same material.
- Each conversation stores its own access level (`ask`, `read-only`, `workspace`, or `full`). A paired device's policy is the hard upper bound; selecting a broader chat level cannot bypass it.
- Conversations support pin/unpin, pinned-first ordering, rename, archive, delete, and right-click/long-press actions.
- Chat and Files both accept multiple files and drag-and-drop. Uploads go directly to the selected workspace or shared storage without spending model tokens.
- Active jobs expose stop and steering controls. New instructions are queued at safe orchestration boundaries.
- Voice input works on desktop and Android; the wake-claim handshake gives the PC priority when PC and phone hear “미스터 로봇” together.
- Calendar/scheduler, Tailscale transport, MCP host, CTF classifier, Docker sandbox, and Orca delegation are modular plugins. Orca is off by default.
- The first-run dependency wizard detects and can install/update the allowlisted Node, Git, Codex, Claude Code, Tailscale, and Docker dependencies.

### Final verification

- `npm run typecheck`: shared, agent, web, and mobile passed.
- `npm run build`: passed.
- `npm test`: smoke, scheduler, AI loop, dependencies, routing, CLI resolution, workspace/cache/steering, pinning, and conversation access passed.
- Browser QA: model and scenario switching, workspace picker, per-chat access control, schedules/calendar, file manager, plugin metadata, and Orca-off default passed without console errors.
- Hardened Docker run: toolbox imports for pwn, angr, and z3 passed with network disabled, read-only root, dropped capabilities, PID/memory/CPU limits, and no-new-privileges.
- Installer SHA-256: `600E4DC4DA938C57C312B3F3D2FDF193A418B99B54ABE24154BF9E2829A809AB`.
- APK SHA-256: `CAB43224B3670A9D0ED54C086C7311DC62F82E2D384964A46E1B3744F0C4F58D`.

### External boundaries

- Google Calendar cloud sync needs a user-owned OAuth client. Local calendar and ICS import/export are ready without OAuth.
- The Windows installer and Android APK are suitable for local testing/sideloading but need production certificates before public distribution.
- Physical-phone tests remain for camera pairing, microphone behavior, background wake policy, and away-from-LAN transfer.
- Credentials remain outside source and docs. Do not add plaintext provider keys, device secrets, PINs, or account tokens to the work-product archive.
