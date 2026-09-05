# Mr.Robot 0.4.4 session handoff — 2026-09-04

## 한 문장 재개

0.4.4는 공개 0.4.3의 보안 도구 포털·토큰 예산 안정화를 유지하면서, PC와 모바일 입력창의 권한·추론 선택 UX, Android 키보드 겹침 보정, PC 전체 브랜드 아이콘 통일을 합친 Windows 통합 릴리스입니다.

## 핵심 변경

- Windows 입력 카드 안에서 대화별 액세스 권한과 추론 강도를 바로 고릅니다. 실행·설정 저장 중에는 선택을 잠그고 기기 권한 상한을 넘는 항목은 비활성화합니다.
- 모바일 0.4.1 소스는 같은 입력 카드 UX, 인증 응답의 `isAdmin`·`permissionCap` 보존, 실제 IME 겹침 좌표 기반 composer 보정을 포함합니다.
- PC 창, 작업 표시줄, Alt+Tab, 트레이, EXE, 설치 마법사와 웹 브랜드 표시는 모바일 전체 아이콘과 같은 `assets/brand/icon.svg` 계열 자산을 사용합니다. 과거 32px 내장 아이콘은 제거했습니다.
- 0.4.1~0.4.3의 Resource Archiver, SSL/TLS Inspector, Runtime Hook, 보호된 도구 포털과 전체 실행 토큰 예산·실패 재시도 중복 방지 변경을 모두 유지합니다.
- PC는 원격 플러그인이 꺼져 있어도 로컬 Agent로 독립 실행합니다. Orca, Tailscale, 원격 연결과 도구 포털은 기본 OFF입니다.

## 권한과 보안 경계

- 실제 실행 권한은 `PC 전역 안전 모드 ∩ 연결 기기 상한 ∩ 대화 선택`입니다.
- Cloudflare Named Tunnel은 정확한 HTTPS origin, 호스트 전체 Access 보호와 전용 Service Token을 요구하고 익명 origin 도달 시 fail closed합니다.
- 도구 포털은 기본 OFF이며 비밀번호 원문을 저장하지 않습니다. 외부 공개는 검증된 Named Tunnel/Access origin에서만 허용합니다.
- Provider key, Tunnel token, Access ID/Secret, pairing secret/PIN, 기기 token, DPAPI/SecureStore 상태와 사용자 데이터는 저장소·릴리스·로그에 포함하지 않습니다.

## 버전과 산출물

- Windows: 0.4.4 (`release/Mr.Robot-Setup-0.4.4-x64.exe`)
- Android: 검증된 0.4.1 / versionCode 16 유지 (`release/mobile/Mr.Robot-Mobile-0.4.1.apk`)
- Windows 설치본의 정확한 크기·SHA-256과 설치 검증 결과는 최종 빌드 뒤 `docs/SESSION_STATE.json`과 `docs/RELEASE_NOTES_0.4.4.md`에 기록합니다.

## 완료 전 검증

- 충돌 표식·JSON 파싱·타입 검사·전체 테스트·누수 테스트·production audit·공개 릴리스 감사를 통과해야 합니다.
- Windows 설치본의 ProductVersion, build/설치본 `app.asar` 동일성, 로컬 `/api/ping`, 정상/좁은 창 UI와 모바일 아이콘 표시를 확인합니다.
- 공개 전 Git LFS pointer와 source ZIP을 다시 감사하고 GitHub Release에는 자격증명이 없는 설치본·source ZIP·checksum만 첨부합니다.

## 외부 경계

- Windows 설치본은 Authenticode 인증서가 없어 SmartScreen 경고가 표시될 수 있습니다.
- Cloudflare DNS·Access 정책은 외부 상태이므로 대시보드 정책은 문서의 fail-closed 구성을 유지해야 합니다.
- 다른 로컬 저장소 remote URL에서 노출된 GitHub PAT는 이 저장소와 별개로 폐기·재발급하고 URL에서 제거해야 합니다.
