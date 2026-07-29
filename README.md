# 🔐 Cipher Inspector

웹 모의해킹·점검용 **AES 암복호화 워크벤치**. 단일 HTML 파일로, 별도 설치 없이 브라우저에서 바로 동작합니다.

**▶ 라이브 데모: https://zz1231me.github.io/web-cipher-inspector/**

대상 앱의 JS에서 찾은 키·IV·방식을 입력하면, Burp 등에서 복사한 암호문을 즉석에서 복호화하고 변조한 평문을 다시 암호화(재전송용)할 수 있습니다.

---

## 주요 기능

- **🪄 자동 디코드 (Magic)** — 붙여넣으면 CyberChef의 Magic처럼 가장 그럴듯한 디코드 레시피를 자동으로 찾아 연쇄 적용하고 결과를 보여줍니다. Base64 · Base64url · Hex · Base32 · URL · gzip/zlib를 재귀적으로 관통 (예: `Base64 → gzip → JSON`).
- **📊 시각적 종합 분석** — 암호문을 넣으면 "무엇인지 · 어떻게 구성됐는지 · 신뢰도(%)"를 아이콘 판정 + 신뢰도 막대 + 레이어 파이프라인 + 엔트로피 게이지 + 블록 정렬/선두 바이트 타일로 시각화. 매직넘버(gzip·PNG·PDF·DER·ZIP…), 토큰(JWT·JWE·PASETO·Fernet), 시크릿(Stripe·GitHub·AWS·bcrypt…), 해시(MD5·SHA·ObjectId), UUID/ULID 자동 식별.
- **🔑 사전 공격** — `.txt` 단어목록을 올려 OpenSSL/CryptoJS passphrase 암호문을 무차별 대입 복호화. 발견 시 비밀번호와 평문을 자동 표시.
- **양방향 라이브 변환** — 평문 ⇄ 암호문이 한 화면에. 한쪽에 입력·붙여넣기하면 반대쪽이 자동으로 채워집니다(자동 변환 토글 가능).
- **지원 방식**
  - AES-CBC (key + IV)
  - AES-GCM (key + nonce + 인증 태그 + AAD) — Web Crypto 기반
  - AES-CTR (key + IV)
  - AES-ECB (key)
  - Passphrase (CryptoJS / OpenSSL `U2FsdGVk…` 형식)
- **실전 편의 기능**
  - `IV‖암호문` 자동 분리/결합 (IV가 암호문 앞에 붙는 스킴 대응)
  - 입력 포맷 자동감지 (Hex / Base64)
  - 무작위 IV/Nonce 생성
  - 키·IV 길이 실시간 경고
  - 결과 보기 토글 (UTF-8 / Hex / Base64)
  - 작업 상태 자동 저장(localStorage) · CDN 폴백
- **키/IV/평문 형식** 각각 UTF-8 / Hex / Base64 선택 가능

## 사용법

1. 라이브 데모를 열거나 `index.html`을 브라우저로 엽니다.
2. 대상 앱의 JS 소스에서 `aes / encrypt / key / iv / mode / tag` 등을 검색해 암호화 파라미터를 찾습니다.
3. **방식 · 키 · IV(· 태그/AAD)** 를 입력합니다.
4. 암호문을 붙여넣으면 → 자동 복호화 / 평문을 입력하면 → 자동 암호화됩니다.

## 보안 & 프라이버시

- 모든 처리는 **브라우저 안에서만** 일어나며 어떤 데이터도 외부로 전송되지 않습니다. (CryptoJS만 CDN에서 로드)
- 입력값(키 포함)은 편의를 위해 **해당 브라우저의 localStorage에만** 임시 저장됩니다. 공용 PC에서는 하단 **전체 지우기**로 삭제하세요.

> ⚠️ **인가된 보안 점검·교육 용도로만** 사용하세요. 본인이 권한을 가진 시스템 또는 명시적으로 허가받은 대상에 대해서만 사용해야 합니다.

## 기술 스택

- 순수 HTML/CSS/JS 단일 파일 (빌드 불필요)
- [CryptoJS 4.2.0](https://cdnjs.com/libraries/crypto-js) — CBC/CTR/ECB/Passphrase
- Web Crypto API (`SubtleCrypto`) — AES-GCM

## 라이선스

MIT
