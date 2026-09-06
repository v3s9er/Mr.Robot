# Mr.Robot 0.4.11 session handoff — 2026-09-06

## Current 0.4.11

Read RELEASE_NOTES_0.4.11.md and PUBLIC_RELEASE_AUDIT_0.4.11.md first.
Adds exact live allow_ai ticket gating without widening existing admin-only use.
PC/APK rebuilt as 0.4.11 (Android code 19). User authorized public GitHub release;
no Drive upload requested in this turn. Existing user roles/config remain private
and unchanged. Earlier 0.4.8–0.4.10 notes below describe local work before this
consolidated release. Remote login/native-phone QA limitations remain.

## Current 0.4.10

Read RELEASE_NOTES_0.4.10.md first. Desktop/mobile compact composer and More
settings sheet implemented together with Discord Agent 1.4.0 model ceilings.
No real user limits changed, no bot-access widening, no external publication.
Desktop browser fixture tested at 1100/390px; native phone verification pending.
EXE/APK version 0.4.10, Android code 18. Prior remote tunnel/login blocker remains.

## Latest local work: 0.4.9

Discord Agent 1.4.0 follow-up: `/robot model-limit user ceiling` adds persisted
guild/user model ceilings (spark/mini/luna/terra/sol/astra; show/unlimited).
Existing server-admin-only access changes and bot use retained. Exact model IDs,
catalog/settings/default/direct execution checks, host pre-provider-call guard;
Discord cannot inherit PC routing presets. See integrations/discordbot/README.md
for policy and native-CLI/PC-full-access limitations. No external publication.

Read `RELEASE_NOTES_0.4.9.md` first. Composer controls deduplicated; optical-QR file-only AES-GCM channel added to Agent and mobile. EXE/APK built locally, desktop updated. No GitHub/Drive publication. Real-phone verification outstanding. Production remote address still fails verification (authenticated HTTP 530 observed); Cloudflare dashboard login required. Do not disable Access or claim remote/E2EE end-to-end production validation. File PSK channel does not encrypt chat/command RPC and has no forward secrecy. Previous Discord standalone work retained.

## Discord 독립 플러그인 (로컬 0.4.8 추가 변경)

Discord Agent 1.3.0은 standalone(연결 config.json만 읽는 독립 discord.py 클라이언트)과 legacy(기존 시큐리티봇 단일 클라이언트 호환) 모드로 분리했습니다. 새 설정은 standalone, 과거 설정은 기능 손실 방지를 위해 legacy로 해석합니다. 독립 모드는 기존 bot/client.py, main.py, GUI/뉴스/KTX에 의존하지 않습니다. standalone.py에는 연결정보 읽기, 계정별 OS 임대 잠금과 기존 봇 IPC 포트 충돌 차단이 있습니다. 기존 소스를 호출하는 부분은 legacy_adapter.py에만 있습니다. 이 두 파일과 requirements.txt도 설치본에 포함합니다. 다른 PC의 같은 토큰 실행은 감지하지 못하며, legacy에서 연결 중지는 여전히 함께 실행한 기존 봇도 종료합니다.

검증: Python 37 tests, Agent/Web typecheck, Discord host/session/authority 회귀 테스트, UI contract 통과. 0.4.8 Windows 재빌드·설치 후 standalone 전환, Gateway ready / busy false / error 없음 확인. 실제 두 번째 실행은 duplicate 코드로 차단했습니다. 설치된 ASAR 및 Python 모듈 4개 해시 일치. 기존 연결 config.json은 읽기만 하며 변경하지 않았습니다. GitHub 공개 버전은 여전히 0.4.7이며 이번 요청에서 업로드하지 않습니다. 토큰·개인 경로·서버 ID는 공개 문서에 기록하지 않습니다.

## 0.4.8 로컬 적용

최근 Discord 티켓 메시지 아래로 중지/모델/권한 메뉴를 이동하고 이전 컴포넌트와 View만 정리합니다. `/robot controls`, `/robot model`, 공급자별 모델 발견/페이지/추론 선택을 추가했습니다. 기존 티켓 1개의 최근 봇 메시지에도 메뉴를 반영했으며 내용은 유지했습니다. Windows 0.4.8.0 설치·실행, ASAR 및 두 Python 모듈 소스 일치, Gateway ready, 모델 목록 실제 조회, Python 29개/호스트 Discord·세션·예산/타입/UI·버전 테스트 통과. 이전 허용 서버 핫픽스도 이번 설치본에 포함됐습니다. GitHub는 0.4.7 게시 상태이며 0.4.8 또는 APK를 게시한 것으로 간주하지 마세요.

## 0.4.7 재개 지점

2026-09-06 로컬 후속 수정: Discord Python 브리지가 뉴스 봇의 단일 server_name 대신 호스트의 로컬 allowedGuildIds를 읽고 가입 서버와 교집합으로 권한을 검사합니다. 사용자가 요청한 추가 서버만 로컬 목록에 등록했으며 기존 서버/뉴스 설정은 유지했습니다. 설치된 unpacked bridge.py에도 핫픽스를 적용했습니다. 이 후속 수정은 아직 새 설치본/GitHub 릴리스에 반영되지 않았습니다. 다음 배포 시 포함하세요. 권한 파일 손상/명시적 빈 목록/등록 회수/복수 서버 테스트 포함 Python 24개 통과. 실제 서버 ID와 백업은 사용자 홈의 비공개 저장소에만 있습니다.

Discord 티켓 작업실을 추가했습니다. ai_talk 채널의 [티켓 열기]에서 제목을 제출하면 개인 비공개 스레드가 생기며, 생성한 서버 관리자만 일반 채팅으로 작업하고 모델·추론·권한·보관·삭제를 관리합니다. 사용자 요청 없이 개인 티켓을 자동 생성하지 않습니다. PC의 Discord 플러그인에서 ai_talk 채널·패널 설치도 가능합니다. 모든 채널/사용자 ID와 매핑은 로컬 플러그인 저장소에만 있습니다. [0.4.7 릴리스 노트](RELEASE_NOTES_0.4.7.md)를 우선 읽으세요. 아래는 이전 버전의 역사 기록입니다.

## 0.4.6 재개 지점

질문별 토큰 예산(64k/256k/1M/자동/무제한)과 입력창 선택기를 추가했습니다. 이전 질문 사용량을 차감하지 않습니다. Discord는 등록 서버의 Administrator 권한을 실시간 확인하고 `/robot access`로 사용자별 실행 권한을 선택합니다. full은 명시적 동의가 필요하며 PC의 전체 읽기 전용 잠금은 유지합니다. Discord 토큰 차단 및 chat.start의 일반 RPC 타임아웃을 없앴습니다. 아래 0.4.4~0.4.5 기록의 소유자 전용/네이티브 전용 무제한 설명은 역사 기록이며 현재 정책이 아닙니다. 상세 내용은 [0.4.6 릴리스 노트](RELEASE_NOTES_0.4.6.md)를 확인하세요. 0.4.6은 로컬 빌드 적용 단계이며 GitHub 게시 또는 새 APK 배포를 완료한 것으로 간주하지 마세요.

## 0.4.5 재개 지점

0.4.4 main을 로컬 UI/모바일 수정과 병합했습니다. Discord Agent 플러그인이 기존 로컬 봇을 래핑하며, 별도 공개 포트 없이 소유자 전용 slash 명령을 제한된 로컬 RPC에 연결합니다. 설정은 사용자 홈의 플러그인 저장소에만 있고 공개 소스에는 없습니다. 설치본에는 generic Python bridge만 asar-unpacked로 포함됩니다. 상세 변경/검증은 [0.4.5 릴리스 노트](RELEASE_NOTES_0.4.5.md)를 확인하세요. 아래는 보존한 0.4.4 설계 기록입니다.

## 한 문장 재개

0.4.4는 간단한 요청의 불필요한 사용은 줄이면서 어려운 작업에는 품질 신호에 따라 예산을 넓히고, 내장 네이티브 앱의 로컬 관리자에게만 누적 토큰 차단 없는 감사 모드를 제공하는 실행 정책 릴리스입니다.

## 0.4.4 핵심 변경

- 권한 선택 옆에 대화별 토큰 정책 드롭다운을 추가했습니다.
- 기본 `적응형 · 품질 우선`은 단순 64,000, 표준 192,000, 복합 512,000토큰의 호출 기준에서 시작합니다. 요청 복잡도, 추론 강도, 입력 문맥, 모델 시나리오와 완료된 도구 진행에 따라 제한된 추가 구간을 열며 한 실행의 절대 상한은 8,000,000토큰입니다.
- 기존 15분 누적 토큰 차단은 제거했습니다. 앞선 작업의 사용량이 다음 정상 작업의 입장을 막지 않습니다.
- `무제한 · 감사만`은 내장 네이티브 앱의 로컬 관리자 연결에만 허용됩니다. 네이티브 메인 프로세스가 내장 서버에서 직접 받은 짧은 1회성 증명을 WebSocket 연결에 제시하며, Mr.Robot의 누적 토큰 예산은 실행을 중단시키지 않지만 사용량은 계속 기록합니다.
- 일반 로컬 브라우저, localhost로 위장한 프록시, 원격·프록시 연결, 연결 기기, REST 실행과 다른 기기에서 가져온 동기화 데이터는 항상 적응형으로 강제합니다. 감사 모드는 동기화되는 대화 속성이나 원격 권한으로 승격할 수 없습니다.

## 사용량 감사와 호출 경쟁 방어

- 공급자가 보고한 실제 입력·출력·추론·캐시 토큰과 예상 비용은 원래 값에 기반해 저장합니다.
- 예약량, 사용량을 보고하지 않는 호출과 실패 중 발생한 부분 사용량은 별도의 보수적 `감사 토큰`으로 저장·집계합니다. 감사 토큰을 공급자 청구량이나 비용으로 오인하지 않습니다.
- 복합 시나리오의 병렬 모델 호출은 전역 48개, 로컬 관리자 32개, 연결 기기별 8개로 제한합니다.
- 한 병렬 분기가 실패하면 형제 호출에 중단 신호를 보내고 모두 실제로 종료할 때까지 `allSettled` 방식으로 기다립니다. 실행 정리가 아직 진행 중인 호출 슬롯을 먼저 반환하지 않으므로 실패 경쟁으로 동시 호출 제한을 우회할 수 없습니다.
- 성공·실패 사용량을 정규화해 음수, `NaN`, 무한대와 비정상적으로 큰 공급자 보고가 저장소를 오염시키지 않게 합니다.

## 감사 모드에도 유지되는 안전장치

- `무제한`은 Mr.Robot의 누적 토큰 차단에만 적용됩니다.
- 공급자 호출 수, 실행 시간, 스트림 누적 바이트·행·버퍼, 전송 크기, 동시 실행, 시작 빈도, 사용자 취소, 최대 도구 단계와 무진전 반복 차단은 그대로 유지됩니다.
- OpenAI 호환 및 Anthropic 스트림에는 호스트 deadline과 누적 입력 상한을 적용합니다.
- Runtime Observer의 CDP 입력에는 frame 256 KiB, 세션 4,096 frames/8 MiB, 동시 pending 명령 64개, 런타임 이벤트 512개와 잘못된 binding 호출 상한을 적용합니다.

## 0.4.2~0.4.3에서 유지되는 기능

- `Resource Archiver`: 제공된 HAR/본문을 우선 사용하고, 직접 수집은 명시적 승인 때만 제한된 GET으로 수행합니다. preview, 중복 제거, URL 재작성, SHA-256 manifest와 부분 실패 ZIP을 제공합니다.
- `SSL/TLS Inspector`: `sslscan`의 코드나 결과를 복사하지 않은 독립 구현입니다. quick은 기본 4회 handshake, standard는 cipher 시험 최대 12회로 제한합니다.
- `Runtime Hook`: 격리된 임시 Chrome/Edge 프로필에서 WebCrypto encrypt/decrypt 경계만 관찰합니다. 기본은 메타데이터이며 평문은 별도 동의, 수정은 정확한 리터럴 1회만 허용합니다.
- 각 플러그인은 카드 안의 전용 작업 화면과 독립 URL(`/tools/resource-archiver`, `/tools/sslscan`, `/tools/runtime-hook`)을 제공합니다.
- 독립 포털은 네이티브 앱에서만 설정하며 scrypt 기반 암호 verifier만 저장합니다. 로그인 세션은 HttpOnly 쿠키와 포트별 `sessionStorage` request proof가 모두 있어야 사용할 수 있습니다.
- 외부 공개는 검증된 정확한 Cloudflare Named Tunnel/Access origin에서만 허용하며 익명 probe가 origin에 닿으면 fail closed로 중지합니다.
- GitHub Secret scanning과 Push protection은 활성화 상태입니다.

## 검증 범위

- Shared/Agent/Web/Mobile 타입 검사와 변경 경로 빌드
- 적응형 예산, 네이티브 로컬 관리자용 1회성 감사 권한 증명과 localhost 재작성 프록시 차단, 원격·연결 기기 강제 적응형 집중 테스트
- 실제 사용량과 감사 토큰 분리, 실패 중 부분 사용량 지속성, 병렬 형제 중단·종료 대기와 공급자 호출 동시성 집중 테스트
- 공급자 무한 스트림, Runtime Observer 반복 입력, 데스크톱·모바일·반응형 UI 계약 검사
- 권한 옆 토큰 정책 드롭다운의 실제 화면 확인

0.4.2에서 통과한 전체 제품 회귀를 반복하지 않고 0.4.4에서 바뀐 실행·사용량·전송·UI 경로를 집중 검증합니다.

## 배포 산출물

- Windows x64: `release/Mr.Robot-Setup-0.4.4-x64.exe`
- 공개 소스: `release/Mr.Robot-source-0.4.4.zip`
- 체크섬: `release/SHA256SUMS-0.4.4.txt`
- Windows 설치 파일은 Authenticode 미서명입니다. 최종 크기는 96,122,658바이트, SHA-256은 `EDC5CE0AA2C84394945281ACB52491BFEB149BC6164273AF9D523F433876399E`입니다. 설치된 앱은 ProductVersion `0.4.4.0`이며 build/설치본 `app.asar`는 모두 `4C3CB162DAC0072740BDA610D5F2B78CFB379A94B5B066BEE0454C9A290E6C5E`로 일치하고 `/api/ping` HTTP 200을 확인했습니다.
- Android는 검증된 기존 `Mr.Robot-Mobile-0.4.0.apk`(versionCode 15)를 유지합니다.

## 외부 경계와 다음 설정

- 사용자가 실제 포털 도메인과 암호를 제공하지 않았으므로 값을 추측하지 않았고 포털은 기본 OFF입니다. 네이티브 앱 설정에서 정확한 HTTPS origin과 암호를 입력한 뒤 켜야 합니다.
- Windows 설치본은 Authenticode 인증서가 없어 SmartScreen이 표시될 수 있으므로 GitHub Release의 SHA-256으로 검증합니다.
- Content-Length가 없는 runtime 응답은 마지막 전달 chunk만큼 상한을 넘은 뒤 감지될 수 있으며 감지 즉시 세션을 종료합니다.
- 브라우저 종료는 부모 프로세스와 임시 프로필을 정리하지만 Windows Job Object 기반의 강제 프로세스 트리 회수는 아직 적용하지 않았습니다.
- 외부 확장 프로그램과 `sslscan` 소스는 열람·복사·번들하지 않았습니다.

## 민감 상태 규칙

포털 암호/검증자, request proof, Provider key, Tunnel token, Access Client ID/Secret, 기기 bearer token, DPAPI ciphertext, SecureStore export와 사용자 데이터는 GitHub, source ZIP, Release, QR, 로그, 문서에 게시하지 않습니다.
