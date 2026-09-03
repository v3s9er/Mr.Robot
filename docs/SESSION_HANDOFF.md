# Mr.Robot 0.4.2 session handoff — 2026-09-03

## 한 문장 재개

0.4.2는 사용자 소유·명시적 허가 대상만 다루는 저트래픽 보안 도구 3종을 `모의해킹` 카테고리와 독립 암호 포털에 추가한 릴리스입니다. 포털은 기본적으로 꺼져 있고, 네이티브 앱에서 정확한 도메인과 암호를 설정해야 열립니다.

## 0.4.2 핵심 변경

- `Resource Archiver`: 제공된 HAR/본문을 우선 사용하고, 직접 수집은 명시적 승인 때만 제한된 GET으로 수행합니다. preview, 중복 제거, URL 재작성, SHA-256 manifest와 부분 실패 ZIP을 제공합니다.
- `SSL/TLS Inspector`: `sslscan`을 호출하거나 소스를 포함하지 않은 독립 구현입니다. quick은 기본 4회 handshake, standard는 cipher 시험 최대 12회로 제한합니다.
- `Runtime Hook`: 격리된 임시 Chrome/Edge 프로필에서 WebCrypto encrypt/decrypt 경계만 관찰합니다. 기본은 메타데이터이며 평문은 별도 동의, 수정은 정확한 리터럴 1회만 허용합니다. HTTPS 443, 정확한 대상, DELETE 차단, 최대 512개 런타임 이벤트를 적용합니다.
- 각 플러그인은 카드 안의 전용 작업 화면과 독립 URL(`/tools/resource-archiver`, `/tools/sslscan`, `/tools/runtime-hook`)을 제공합니다.
- 독립 포털은 네이티브 앱에서만 설정하며, scrypt 기반 암호 verifier만 저장합니다. 로그인 세션은 HttpOnly 쿠키와 포트별 `sessionStorage` request proof가 모두 있어야 사용할 수 있습니다.
- 외부 공개는 검증된 정확한 Cloudflare Named Tunnel/Access origin에서만 허용하며, 익명 probe가 origin에 닿으면 fail closed로 중지합니다.
- 설정 변경, 로그아웃, 만료, 포털 중지 시 세션·실행·observer·아티팩트를 폐기합니다.
- GitHub Secret scanning과 Push protection은 모두 활성화 상태입니다.

## 저트래픽·안전 상한

- Resource Archiver 포털 실행은 최대 20개 네트워크 요청과 16 MiB ZIP을 허용하고, preview에서는 추가 네트워크 요청을 하지 않습니다.
- TLS 포털은 quick 또는 standard만 제공하며 standard cipher 시험은 최대 12개입니다.
- Runtime Hook의 모든 CDP 입력은 JSON 파싱 전에 frame 256 KiB, 세션 4,096 frames/8 MiB, 동시 pending 명령 64개로 제한합니다.
- Runtime Hook은 키보드·DOM·비밀번호를 수집하지 않고, `WebSocketStream`을 포함한 우회 트래픽 경로를 차단합니다.
- 모든 실행은 대상 소유·허가 확인과 실행별 승인을 요구합니다.

## 검증 결과

- `npm run typecheck`, `npm run build`, 전체 `npm test` 통과
- Tool Portal 11/11, Runtime Observer 23/23, SSL/TLS Inspector 7/7, 포털 클라이언트 5/5 통과
- `npm run test:leak`: `NO LEAK DETECTED`, total drift 1,468 KB
- root와 mobile production dependency audit: 각각 0 vulnerabilities
- 데스크톱·모바일 포털 UI와 접근성/반응형 검증 통과
- 공식 `sslscan` 2.2.2를 격리된 localhost fixture에서 새로 실행해 TLS 1.2/1.3, 인증서 주체, TLS 1.2 cipher 2개가 일치함을 확인
- 같은 fixture의 TCP 연결 수: Mr.Robot quick 4, quick cache hit 0, deep 31, 공식 `sslscan` 80
- Windows 설치본 ProductVersion 0.4.2.0 확인, build와 설치본 `app.asar` SHA-256 일치, 로컬 `/api/ping` 200 확인

## Windows 산출물

- `release/Mr.Robot-Setup-0.4.2-x64.exe`
  - 96,116,218 bytes
  - SHA-256 `0B80297B0571FEB657E95814DD7DA3CEC7F970F14F459447443B6BA27428FEC9`
  - ProductVersion 0.4.2.0, Authenticode 미서명
- build/설치본 `app.asar` SHA-256
  - `DDFD11A1D7B640A772D8D44FD422762D6B7B6D1A900E8362D33375C6D970D802`
- Android는 기존 0.4.0 APK를 유지합니다.
- Source ZIP과 `SHA256SUMS-0.4.2.txt`는 태그 커밋에서 생성해 GitHub Release에 첨부합니다.

## 외부 경계와 다음 설정

- 사용자가 실제 포털 도메인과 암호를 제공하지 않았으므로 값을 추측하지 않았고, 포털은 기본 OFF입니다. 네이티브 앱 설정에서 정확한 HTTPS origin과 암호를 입력한 뒤 켜야 합니다.
- Windows 설치본은 Authenticode 인증서가 없어 SmartScreen이 표시될 수 있으므로 GitHub Release의 SHA-256으로 검증합니다.
- Content-Length가 없는 runtime 응답은 마지막 전달 chunk만큼 상한을 넘은 뒤 감지될 수 있으며, 감지 즉시 세션을 종료합니다.
- 브라우저 종료는 부모 프로세스와 임시 프로필을 정리하지만 Windows Job Object 기반의 강제 프로세스 트리 회수는 아직 적용하지 않았습니다.
- 외부 확장 프로그램과 `sslscan` 소스는 열람·복사·번들하지 않았습니다.

## 민감 상태 규칙

포털 암호/검증자, request proof, Provider key, Tunnel token, Access Client ID/Secret, 기기 bearer token, DPAPI ciphertext, SecureStore export와 사용자 데이터는 GitHub, source ZIP, Release, QR, 로그, 문서에 게시하지 않습니다.
