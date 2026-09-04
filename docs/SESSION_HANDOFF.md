# Mr.Robot 0.4.4 session handoff — 2026-09-04

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
