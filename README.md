# 🔐 Cipher Inspector

웹 모의해킹·점검용 **AES 암복호화 워크벤치**. 단일 HTML 파일로, 별도 설치 없이 브라우저에서 바로 동작합니다.

**▶ 라이브 데모: https://zz1231me.github.io/web-cipher-inspector/**

대상 앱의 JS에서 찾은 키·IV·방식을 입력하면, Burp 등에서 복사한 암호문을 즉석에서 복호화하고 변조한 평문을 다시 암호화(재전송용)할 수 있습니다.

---

## 주요 기능

- **자동 해독 (Auto-solve)** — 붙여넣으면 여러 디코드 조합을 폭넓게 탐색(BFS)해 읽을 수 있는 평문 후보를 순위로 제시. Base64 · Base64url · Hex · Base32 · Base58 · Ascii85 · URL · Quoted-printable · HTML엔티티 · gzip/zlib/deflate에 더해 **단일바이트/반복키 XOR**·**Caesar/ROT-N/ROT47**·**Atbash**·**Vigenère 자동해독**까지 자동 시도 (예: `Hex → XOR 0x42 → 평문`, `Base64 → gzip → JSON`). 입력 카드의 체크박스로 각 자동작업을 켜고 끕니다.
- **문자코드·이스케이프·고전암호·레거시 인코딩** — 구분자 있는 문자코드 열(10/16/8/2진), `\xHH \uHHHH \u{..} \OOO` 이스케이프, A1Z26, Morse, NATO 포네틱, Base45(RFC 9285), uuencode, Punycode(`xn--`), 제로폭 문자 스테가노까지 자동 인식·복원.
- **입력 자동화 · 결과 저장** — 클립보드 가져오기 버튼, 암호문 칸에 **파일 드래그&드롭**(바이트를 읽어 자동 분석), 결과를 `.txt/.bin`으로 **파일 저장**. 키 미입력 시 흔한 상수 키(all-zero·`YELLOW SUBMARINE`·`password`…)를 자동 대입하는 **흔한 키 자동시도**.
- **사전 공격 · 크랙** — `.txt` 단어목록으로 OpenSSL/CryptoJS passphrase, 현재 방식(AES 키), 그리고 **bcrypt · argon2 해시**(hash-wasm)를 자동 대입. 암호문/해시를 넣고 목록만 올리면 자동 실행.
- **추가 암호 · 도구** — AES 외 RC4 · DES · Triple DES. 고급 도구에 해시 8종(MD5·SHA1·SHA256·SHA512 + SHA3·RIPEMD160·BLAKE2b·CRC32), XOR(키/브루트), ROT-N, 공유 링크(#s=), openssl/python 코드 내보내기.
- **반복키 XOR 자동해독** — Vigenère식 반복키 XOR을 정규화 해밍(Friedman)으로 키 길이 추정 후 열별 문자빈도 브루트로 자동 복원. 자동 해독에 편입 + 고급 도구 수동 버튼.
- **JWT 서명 검증** — jsrsasign으로 HS256/384/512, RS·ES 서명 검증(HS는 비밀키, RS/ES는 PEM 공개키).
- **RSA 개인키 복호화** — node-forge로 PEM 개인키(암호화 PEM은 비밀번호) 로드 후 RSA-OAEP/PKCS1 자동 복호화.
- **오픈소스 활용(지연 로드)** — [CryptoJS](https://github.com/brix/crypto-js) 코어, [hash-wasm](https://github.com/Daninet/hash-wasm)(SHA3/RIPEMD/BLAKE2/CRC32 · bcrypt/argon2), [jsrsasign](https://github.com/kjur/jsrsasign)(JWT 검증), [node-forge](https://github.com/digitalbazaar/forge)(RSA/PEM).
- **📊 시각적 종합 분석** — 암호문을 넣으면 "무엇인지 · 어떻게 구성됐는지 · 신뢰도(%)"를 아이콘 판정 + 신뢰도 막대 + 레이어 파이프라인 + 엔트로피 게이지 + 블록 정렬/선두 바이트 타일로 시각화. 매직넘버(gzip·PNG·PDF·DER·ZIP…), 토큰(JWT·JWE·PASETO·Fernet), 시크릿(Stripe·GitHub·AWS·bcrypt…), 해시(MD5·SHA·ObjectId), UUID/ULID 자동 식별.
- **🚀 원클릭 "전부 시도"** — 버튼 하나로 분석·자동해독·해시 생성·크랙을 한 번에 실행하고 종합 결과를 한 곳에 모아 **전체 복사/리포트 저장**. 크랙은 **내장 공용 비밀번호 목록**을 기본 사용해 파일 업로드 없이 바로 동작(자기 `.txt`를 올리면 교체).
- **붙여넣기 즉시 자동 판정 + 세션 특성 분석** — Burp 등에서 뽑은 세션·쿠키·정체불명 값을 붙여넣으면 무엇인지·신뢰도·구성이 자동 판정됩니다. 프레임워크 세션을 폭넓게 식별하고 **각 세션의 정체·구조·보안 특성(어떤 공격이 유효한지)** 까지 해설합니다:
  - **JWT / JWE**, **Flask/itsdangerous**, **Django**, **Rails**(Marshal/JSON `--` HMAC), **Laravel**(암호화 `{iv,value,mac}`), **Express connect.sid**, **ASP.NET ViewState**, **JSESSIONID / PHPSESSID**(불투명 서버측 세션), UUID/ULID·Fernet·PASETO·각종 시크릿/해시.
  - JWT는 결과 카드에서 **alg=none 위조·서명 크랙**을 바로 실행. `자동 브루트포스`를 켜두면 해시/암호문/JWT를 붙여넣는 즉시 **0클릭으로 내장 목록 크랙**까지 수행합니다.
- **🔑 사전 공격** — 내장 목록 또는 올린 `.txt` 단어목록으로 passphrase·AES 키·해시·JWT 비밀키를 무차별 대입. 발견 시 비밀번호와 평문을 자동 표시.
- **양방향 라이브 변환** — 평문 ⇄ 암호문이 한 화면에. 한쪽에 입력·붙여넣기하면 반대쪽이 자동으로 채워집니다(자동 변환 토글 가능).
- **지원 방식**
  - AES-CBC / CTR / CFB / OFB / ECB (key + IV)
  - AES-GCM (key + nonce + 인증 태그 + AAD) — Web Crypto 기반
  - **ChaCha20-Poly1305** (key + nonce + 태그) — 순수 JS, RFC 8439 검증
  - **Fernet** (base64url 32B 키 → AES-128-CBC + HMAC-SHA256 검증)
  - Passphrase — OpenSSL `U2FsdGVk…`의 **MD5·SHA-256 EVP_BytesToKey** 및 **PBKDF2(SHA-256/1)** 를 AES-256/128로 자동 시도 (최신 `openssl enc` 파일 대응)
  - **JWE 복호화** (dir·RSA-OAEP·RSA-OAEP-256·RSA1_5 × A128/256GCM·A128CBC-HS256·A256CBC-HS512)
  - **키·인증서 파싱** — X.509, 암호화 PKCS#8, PKCS#12(.pfx)
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
