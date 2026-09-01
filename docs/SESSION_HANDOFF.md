# Mr.Robot 0.3.9 session handoff — 2026-09-01

## 한 문장 재개

0.3.9는 Cloudflare Named Tunnel + Access fail-closed 경계, 기본 OFF built-in Remote Link, 고정 모체 없는 PC 1·PC 2·휴대폰 등록, 안전한 실행 PC 전환, 최신 EXE/APK 재빌드·재설치와 실제 외부 WSS·자동 시작 검증까지 완료됐습니다. 남은 릴리스 작업은 이 문서를 포함한 clean `HEAD`의 소스 ZIP·체크섬 생성과 승인된 네 파일의 GitHub/비공개 Drive 게시뿐입니다.

## 0.3.9에서 완료된 구현

- PC를 상하 관계로 묶지 않습니다. 각 PC는 독립된 실행 호스트이며 PC별 등록 정보, bearer token, 연결 origin, Cloudflare Access origin을 별도로 보관합니다.
- 데스크톱 대화 상단과 프로필, Android 홈 화면에서 현재 실행 PC를 명시적으로 선택할 수 있습니다. PC 1·PC 2·휴대폰 한 대 구성을 저장하고 전환하는 계약 테스트가 추가됐습니다.
- 데스크톱은 유효한 마지막 원격 실행 PC를 재시작 뒤 복원하고, 삭제·연결 실패 시 해당 선택을 지운 뒤 내장 로컬 Agent로 fail-safe fallback합니다.
- 모바일 PC 전환은 이전 연결의 generation을 무효화한 뒤 소켓과 재연결 타이머를 정리합니다. 이전 PC의 지연된 close·연결 완료가 새 선택을 덮는 race를 차단합니다.
- 실행 중인 run이 하나라도 있으면 데스크톱 상단·프로필과 Android 선택 창의 PC 전환·연결 관리를 모두 잠급니다. 완료 또는 명시적 중지 뒤에만 전환해 이전 PC 작업이 보이지 않게 계속되는 상태를 막습니다.
- 데스크톱은 등록된 원격 PC가 없어도 로컬 Agent를 단독 사용합니다. Remote Link를 켜지 않아도 채팅·도구·로컬 파일 기능은 동작합니다.
- `remote-link`는 `packages/agent/src/plugins/remote-link.ts`의 별도 수명주기와 권한을 가진 선택형 built-in 플러그인 모듈이며 기본값은 OFF입니다. 현재 배포 형태는 EXE 내장형이고, 별도 패키지로 설치·업데이트하는 외부 플러그인은 아닙니다.
- Named Tunnel은 사용자 호스트명, Tunnel 토큰, Access Service Token, 자동 시작을 지원합니다. 플러그인이 활성화되고 자동 시작을 선택한 경우 저장된 Named Tunnel을 복구한 뒤 동일한 외부 검증을 수행합니다.
- 외부 다중 PC는 PC마다 고유한 hostname·전용 Tunnel을 사용합니다. 같은 hostname을 두 Connector가 공유하면 Cloudflare가 독립 실행 대상을 구분할 수 없으므로 UI에서 금지 안내를 제공합니다.
- Named Tunnel 시작 검증은 익명 ping·WebSocket 티켓·pair 경로가 Agent까지 도달하지 않는지 확인하고, Service Token 요청에서만 정확한 Agent 응답을 요구합니다. 실패하면 Tunnel을 중지해 공개 또는 부분 보호 상태로 남지 않습니다.
- Service Token 헤더는 등록된 정확한 HTTPS origin에만 사용합니다. 공개 접미사 기준으로 다른 소유 도메인, 유사 도메인, 평문 HTTP, 비표준 대상에 전달하지 않습니다.
- Windows 자격증명은 CurrentUser DPAPI, Android의 PC별 자격증명은 SecureStore/Keystore에만 저장합니다. QR·로그·상태 응답·renderer·명령줄에는 장기 secret을 넣지 않습니다.
- 공개 릴리스 감사가 추적 소스, 전체 도달 가능 Git 이력, LFS 포인터, Desktop stage를 검사합니다. 미추적 소스가 남아 있거나 비밀 패턴이 발견되면 소스 ZIP 생성을 거부합니다.
- 저장소 루트가 런타임 홈으로 잘못 사용되어도 설정, 대화, 플러그인 상태, 공유 파일, 서명키가 Git에 들어가지 않도록 ignore 경계를 보강했습니다.

## 유지되는 0.3.8 보안 경계

- 공개 페어링은 일반 로컬 PIN과 분리된 메모리 전용 1회용 handoff를 사용합니다.
- 공개 WebSocket은 짧은 수명의 source·host·principal 바인딩 1회용 티켓, Origin 검사, 연결·메시지·바이트·동시 RPC 제한을 사용합니다.
- 관리자 secret과 MCP 환경값은 목적별 DPAPI envelope로 분리하고, renderer에는 관리자·원격 PC bearer 자격증명을 반환하지 않습니다.
- 파일·작업 전송은 1회용 범위 제한 grant, capability, 동시성, rolling byte, 응답 크기, 여유 공간, 공유 영역 제한을 유지합니다.
- 모델 시작은 REST와 WebSocket이 동일한 권한·빈도·동시성·토큰 회계 admission을 사용하며 취소와 실패 때 예약을 반환합니다.
- 개인 업무 캘린더, XLSX 가져오기, 모델·추론 선택, 프리셋, 모바일 키보드 대응, MCP, 음성, CTF/Docker 기능을 유지합니다.

## 완료된 릴리스 검증

- 다중 PC 변경과 전환 잠금을 포함한 `npm run typecheck`, production build, 전체 `npm test`, UI/mobile 계약 테스트, `git diff --check`가 통과했습니다.
- `npm run test:leak`는 `NO LEAK DETECTED`, total drift 1,466 KB로 통과했습니다.
- root와 mobile의 production dependency audit는 각각 0 vulnerabilities입니다.
- 공개 릴리스 감사는 추적 소스 214개, Git LFS 릴리스 포인터 22개, 도달 가능한 Git 이력과 Desktop stage에서 실제 자격증명 유출을 찾지 않았습니다.
- 실제 외부 경계에서 익명 ping·ws-ticket·pair 차단, Service Token ping HTTP 200과 정확한 Agent 응답, 1회용 ws-ticket HTTP 200을 확인했습니다.
- 외부 `wss://` 연결에서 `mr-robot-rpc-v1` 협상, 관리자 auth와 status RPC가 성공했습니다.
- 최종 설치본을 재시작한 뒤 Named Tunnel이 자동 복구되어 `running`, `reachable`, `accessProtected`, `autoStart`가 모두 true이고 오류가 없음을 확인했습니다.
- 설치된 Desktop 0.3.9.0의 실행 파일과 `app.asar`가 최종 `release/win-unpacked` 산출물과 SHA-256·크기 기준으로 일치합니다.
- Android 0.3.9/versionCode 14는 기존 공식 signer 연속성과 APK Signature Scheme v2를 확인했습니다.

## 최종 게시 단계

1. 이 문서를 커밋하고 final clean `HEAD`에서 공개 감사를 다시 실행한 뒤 source ZIP을 생성합니다.
2. EXE·APK·source ZIP 세 파일의 SHA-256을 `SHA256SUMS-0.3.9.txt`에 기록합니다.
3. 정확히 이 네 파일만 GitHub Release와 소유자 전용 Google Drive 폴더에 게시하고 크기·이름을 다시 확인합니다. `release/*` 와일드카드는 사용하지 않습니다.

## 산출물 상태

- Windows x64 installer: 96,036,171 bytes, SHA-256 `4E652F32AB7ED00860D6C0C78C28DEF16553D5308AF82C7192D1FEBBFD41B84E`, Authenticode 미서명
- Android APK: 87,558,760 bytes, SHA-256 `C45AE3DFB93DD0CEF3ED6C6AF1B050EBD37E5A6835117E9D91994AAE1F3D0963`, versionName `0.3.9`, versionCode `14`, 공식 signer·v2 검증 완료
- Source archive와 통합 checksum: 이 문서를 포함한 final clean `HEAD` 커밋 뒤 생성

## 남은 외부 경계

- Windows 설치 파일은 Authenticode 서명이 없어 SmartScreen 경고가 나타날 수 있습니다. 게시 뒤 제공되는 SHA-256을 설치 전에 확인해야 합니다.
- 직접 설치하는 도구 버전과 음성 asset은 고정·검증하지만, 모든 transitive npm/pip 의존성을 별도 hash lock으로 검증하지는 않습니다.
- 구독형 native CLI는 provider가 단일 실행의 강제 token 상한을 제공하지 않으므로 Mr.Robot이 시간·출력·동시성·시작 횟수·사후 회계로 제한합니다.
- 이전 대화에 평문으로 노출된 DeepSeek 키는 저장소 정리로 폐기할 수 없습니다. 사용자가 provider 콘솔에서 반드시 revoke하고 재발급해야 합니다.

## 민감 상태 규칙

Provider 키, pairing secret/PIN, handoff code, Tunnel 토큰, Access Client ID/Secret, 원격 PC bearer token, 서명키, DPAPI ciphertext, SecureStore export, 개인 workbook 데이터, 내부 링크와 업무 식별자는 GitHub, 소스 ZIP, Google Drive 릴리스 폴더, QR, 로그, 문서에 게시하지 않습니다. 플러그인은 코드만 공개하며 런타임 설정과 자격증명은 사용자별 보안 저장소에 남깁니다.

## 빠른 최종 검증

```powershell
npm run typecheck
npm run build
npm test
npm run test:leak
npm audit --omit=dev
npm audit --omit=dev --prefix apps/mobile
npm run audit:public
npm run release:source
```
