# Mr.Robot 0.4.1 session handoff — 2026-09-02

## 한 문장 재개

0.4.1은 플러그인 카테고리와 페이지 내부 작업 화면을 추가하고, 독립 구현한 저트래픽 Resource Archiver와 SSL/TLS Inspector를 `모의해킹` 카테고리에 통합한 릴리스입니다. 0.4.0의 원격 연결·모바일 보안 경계는 그대로 유지합니다.

## 0.4.1 핵심 변경

- GitHub 저장소의 Secret scanning과 Push protection을 모두 활성화했습니다.
- 플러그인을 시스템·연결, 생산성, 개발, 모의해킹, 기타로 묶고 관리자 변경값을 호스트 설정에 유지합니다.
- 플러그인 카드에서 전용 작업 화면을 열어 입력, preview, 승인, 진행률, 요약과 원본 JSON을 한 흐름에서 다룰 수 있습니다.
- Authorized Web Resource Archiver는 제공된 HAR/브라우저 본문을 우선 사용하고 직접 네트워크 수집은 명시적으로 켠 경우에만 제한된 GET으로 수행합니다. URL 재작성, SHA-256 manifest, 중복 제거, 부분 실패 ZIP을 제공합니다.
- SSL/TLS Inspector는 `sslscan`을 포함하거나 호출하지 않는 독립 구현이며, 단일 공개 호스트에 quick 4회 handshake를 기본으로 사용합니다.
- 두 플러그인은 실행별 대상 소유·허가 확인과 승인을 요구하고 DNS 전체 검증·주소 고정·timeout·취소·동시성·총량 상한을 적용합니다.
- 외부 확장 프로그램과 `sslscan` 소스는 열람해 복사하거나 배포물에 포함하지 않았습니다.

## 0.4.0에서 유지되는 기반

- 좁은 창에서도 데스크톱 상단 컨트롤이 겹치지 않도록 우선순위별로 접히고, 프로필·대화 메뉴·프리셋·모델 선택과 모달의 클릭 경계를 정리했습니다.
- Android 채팅은 소프트 키보드 높이와 safe area를 반영해 입력창과 최근 메시지가 가려지지 않으며, 긴 오류·주소·PC 카드가 작은 화면을 밀어내지 않습니다.
- 로컬 PC는 연결 화면을 요구하지 않습니다. 원격 PC가 없거나 연결에 실패해도 현재 PC의 내장 Agent로 안전하게 돌아옵니다.
- PC별 주소와 기기 자격증명을 독립 저장하고, 실행 중 작업이 있을 때 PC 전환을 잠가 보이지 않는 이전 호스트에서 작업이 계속되는 상태를 막습니다.
- Cloudflare Named Tunnel은 정확한 HTTPS origin, 전용 Tunnel, 호스트 전체 Access 보호, 전용 Service Token을 요구하며 익명 ping·pair·WebSocket 경로가 Agent에 닿으면 fail closed로 중지합니다.
- 새 휴대폰 등록 QR은 12자리·10분·1회용 코드와 최대 5분의 서버 결합 Access assertion만 포함합니다. 장기 Access ID/Secret, Tunnel token, 관리자 secret, 기기 token은 QR·renderer·로그에 포함되지 않습니다.
- 등록 assertion은 JSON으로 반환하지 않고 `Secure; HttpOnly; SameSite=Strict` host cookie로 PC 프로세스에만 전달한 뒤, 공식 `cf-access-token` 헤더로 한 번 더 검증합니다.
- 서버는 assertion hash·정확한 origin·PIN·만료를 메모리에 묶고 원자적으로 한 번만 소비합니다. 재사용과 만료 요청은 구분해 거부합니다.
- Android는 PC별 기기 token과 Access 자격증명을 하나의 버전형 SecureStore bundle로 원자 저장합니다. 구형 3-key 저장 형식은 자동 이관되며 저장·메타데이터 실패 때 이전 상태로 롤백합니다.
- Windows의 원격 자격증명은 전용 DPAPI purpose로 분리합니다. 기존 릴리스의 provider-purpose 암호문은 현재 Windows 사용자에서 한 번만 복호화해 새 purpose로 다시 저장합니다.
- cloudflared 설치·동시 시작·자동 시작·중지 수명주기를 보강하고, 동일 상태 이벤트 반복 때문에 플러그인 버튼이 깜빡이는 현상을 줄였습니다.
- 공개 릴리스 감사는 실제 `dpapi:v1:` envelope와 Cloudflare Access secret 형식, Git 이력·LFS pointer·desktop stage를 검사합니다.

## 검증 기준

- `npm run typecheck`, `npm run build`, 전체 `npm test` 통과
- `npm run test:leak`: `NO LEAK DETECTED`, total drift 1,463 KB
- root와 mobile production dependency audit: 각 0 vulnerabilities
- 공개 릴리스 감사: 추적 소스 238개와 추적 릴리스 pointer 전체 검사 통과
- Windows 0.4.1 설치본을 실제 설치한 뒤 실행 파일과 `app.asar`가 build 산출물과 일치하고 로컬 `/api/ping` 성공
- 격리된 로컬 TLS fixture에서 공식 `sslscan` 2.2.2와 TLS 1.2/1.3, 인증서 주체, TLS 1.2 cipher 2개 일치 확인
- 같은 fixture에서 Mr.Robot quick 4회, quick cache hit 0회, deep 31회, 공식 `sslscan` 80회 TCP 연결 관찰
- Android APK: package `com.mrrobot.mobile`, versionCode 15, 기존 공식 signer 연속성, APK Signature Scheme v2 확인
- 실제 `robot.v3s9er.com`에서 익명 요청이 Cloudflare Access 앞단에서 차단되는 것을 확인
- 실제 `robot.v3s9er.com`에서 v5 자동 등록 200, 발급된 기기·Access 자격으로 원격 상태 200, 같은 등록 QR 재사용 409 `PAIRING_CONSUMED`를 확인하고 시험 기기를 즉시 폐기

## 산출물

- Windows x64: `release/Mr.Robot-Setup-0.4.1-x64.exe`
  - 96,075,374 bytes
  - SHA-256 `52CAE1CFFAC788EBED4E46DB191BE8523A2B39B05D268B6499F930253064D691`
  - ProductVersion 0.4.1.0, Authenticode 미서명
- Android: `release/mobile/Mr.Robot-Mobile-0.4.0.apk`
  - 87,579,116 bytes
  - SHA-256 `7F11015348C96736ECB6EC70C22FE58E5C595D71DEF63FED129E0EE1034C54D9`
  - versionName 0.4.0, versionCode 15, APK Signature Scheme v2
  - signer certificate SHA-256 `EB782D956DABCA784D9E0AFC152BF7061ACE72CE805215E3C6502AAE72E1A0E6`
- Source ZIP과 `SHA256SUMS-0.4.1.txt`는 최종 공개 커밋에서 생성합니다.

## Cloudflare 무료 구성 원칙

- Access self-hosted application은 `robot.v3s9er.com` 호스트 전체를 보호합니다.
- 사람용 정책은 소유자 이메일만 Allow, 앱용 정책은 Mr.Robot 전용 Service Token만 Service Auth로 둡니다.
- Bypass, Everyone, Any Access Service Token은 사용하지 않습니다.
- 이 연결 경로에는 유료 Workers·R2·Images·Stream·Log Explorer를 붙이지 않습니다.
- 각 PC는 서로 다른 hostname과 전용 Tunnel을 사용합니다. 같은 hostname으로 여러 PC를 load-balance하지 않습니다.
- PC와 Mr.Robot이 켜져 있을 때만 원격 명령이 실행됩니다. Cloudflare는 Agent 작업을 대신 보관하거나 실행하지 않습니다.

## 유지되는 외부 경계

- Windows 설치본은 상용 Authenticode 인증서가 없어 SmartScreen이 표시될 수 있습니다. GitHub Release의 SHA-256으로 파일을 검증해야 합니다.
- Cloudflare 계정·DNS·Access 정책은 외부 서비스 상태입니다. 앱은 시작할 때 fail-closed 검사를 수행하지만 대시보드 정책 변경 자체를 통제하지는 않습니다.
- 구독형 native CLI는 공급자가 단일 실행의 강제 token 상한을 제공하지 않으므로 시간·출력·동시성·시작 횟수·사후 회계로 제한합니다.
- 과거 대화에 노출된 공급자 API key는 저장소 정리만으로 폐기되지 않습니다. 공급자 콘솔에서 revoke하고 재발급해야 합니다.

## 민감 상태 규칙

Provider key, pairing secret/PIN, handoff code, Tunnel token, Access Client ID/Secret, 기기 bearer token, Android signing key/password, DPAPI ciphertext, SecureStore export, 개인 workbook·캘린더 데이터는 GitHub, source ZIP, GitHub Release, Google Drive 배포 폴더, QR, 로그, 문서에 게시하지 않습니다. 플러그인은 코드만 공개하고 사용자별 설정과 자격증명은 OS 보안 저장소에 남깁니다.

## 빠른 재검증

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
