# Mr.Robot 0.4.1 session handoff — 2026-09-02

## 한 문장 재개

0.4.1은 0.4.0의 원격 연결·보안 경계를 유지하면서 데스크톱과 모바일 입력창 안에 대화별 권한·추론 드롭다운을 넣고, Android 키보드가 입력 커서를 가리는 문제를 실제 좌표 측정으로 보정한 사용성 릴리스입니다. PC는 원격 플러그인 없이도 로컬 Agent로 독립 실행되고, 원격 기능은 사용자가 켠 경우에만 동작합니다.

## 0.4.1 핵심 변경

- Windows와 Android 입력 카드 안에서 대화별 액세스 권한과 추론 강도를 드롭다운으로 바꿉니다. 작은 화면의 가로 추론 칩은 제거했습니다.
- 실행 권한은 계속 `PC 전역 안전 모드 ∩ 연결 기기 상한 ∩ 대화 선택`으로 계산합니다. 모바일이 자신의 기기 상한을 올릴 수 없고, 상한보다 높은 항목은 잠금·안내됩니다.
- Android는 키보드 상단과 composer의 실제 화면 좌표를 측정해 `adjustResize`가 누락한 겹침만 보정합니다. API 36 에뮬레이터에서 두 줄 입력·커서·전송 및 권한/추론 모달을 확인했습니다.
- 현재 활성 휴대폰 링크 상한은 `작업 폴더 자동`으로 올렸습니다. `전체 허용`은 열지 않았고, 각 대화에서는 입력창 권한 드롭다운으로 같은 단계를 한 번 선택해야 합니다.

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
- 공개 릴리스 감사: 추적 소스 214개와 추적 릴리스 pointer 전체 검사 통과
- Windows 설치본을 실제 설치한 뒤 실행 파일과 `app.asar`가 build 산출물과 일치하고 로컬 `/api/ping` 성공
- Android APK: package `com.mrrobot.mobile`, versionCode 16, 기존 공식 signer 연속성, APK Signature Scheme v2 확인
- 실제 `robot.v3s9er.com`에서 익명 요청이 Cloudflare Access 앞단에서 차단되는 것을 확인
- 실제 `robot.v3s9er.com`에서 v5 자동 등록 200, 발급된 기기·Access 자격으로 원격 상태 200, 같은 등록 QR 재사용 409 `PAIRING_CONSUMED`를 확인하고 시험 기기를 즉시 폐기

## 산출물

- Windows x64: `release/Mr.Robot-Setup-0.4.1-x64.exe`
  - 96,044,735 bytes
  - SHA-256 `DAADE9298CEFC6228950A29E93549671C3E692B67F265CDE6D53FAA29497F726`
  - ProductVersion 0.4.1.0, Authenticode 미서명
- Android: `release/mobile/Mr.Robot-Mobile-0.4.1.apk`
  - 87,586,088 bytes
  - SHA-256 `9484669D85064CD4DCDFAAFA4E9E37FE7A376BF12A38445E2B7A097E7EC53351`
  - versionName 0.4.1, versionCode 16, APK Signature Scheme v2
  - signer certificate SHA-256 `EB782D956DABCA784D9E0AFC152BF7061ACE72CE805215E3C6502AAE72E1A0E6`
- Source ZIP과 통합 checksum은 최종 공개 커밋에서 생성합니다.

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
