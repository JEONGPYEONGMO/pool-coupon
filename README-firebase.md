# 🎫 수영장 쿠폰 관리 시스템 (Firebase 실시간 연동)

관리자 앱에서 쿠폰을 관리하면, 회원 앱에서 실시간으로 잔여·내역을 확인할 수 있습니다.

---

## 파일 구성

| 파일 | 대상 | 용도 |
|------|------|------|
| `pool-coupon-admin.html` | 관리자 (현장 태블릿) | 회원 등록, 쿠폰 사용/추가, 전체 관리 |
| `pool-coupon-member.html` | 회원 (개인 폰) | 내 쿠폰 잔여 확인, 사용 내역 조회 (읽기 전용) |

---

## 🔥 Firebase 초기 설정 (최초 1회, 약 10분)

### Step 1: Firebase 프로젝트 생성

1. [Firebase Console](https://console.firebase.google.com/) 접속 → Google 계정 로그인
2. **"프로젝트 추가"** 클릭
3. 프로젝트 이름 입력 (예: `pool-coupon`) → 계속
4. Google Analytics는 **끄기** → **프로젝트 만들기**

### Step 2: Realtime Database 생성

1. 왼쪽 메뉴에서 **"빌드" → "Realtime Database"** 클릭
2. **"데이터베이스 만들기"** 클릭
3. 위치: **싱가포르 (asia-southeast1)** 선택 (한국에서 빠름)
4. 보안 규칙: **"테스트 모드에서 시작"** 선택 → 사용 설정

> ⚠️ 테스트 모드는 30일 후 만료됩니다. 아래 "보안 규칙 설정"을 반드시 진행하세요.

### Step 3: 보안 규칙 설정

Realtime Database → **규칙** 탭에서 아래 내용으로 교체:

```json
{
  "rules": {
    "pool": {
      ".read": true,
      ".write": true
    }
  }
}
```

> 💡 회원은 읽기만 하지만, 앱 구조상 write도 true로 설정합니다.
> 더 엄격한 보안이 필요하면 Firebase Authentication을 추가할 수 있습니다.

### Step 4: 웹 앱 등록 및 설정 복사

1. Firebase Console 왼쪽 상단 **⚙️ (톱니바퀴)** → **프로젝트 설정**
2. 하단 **"내 앱"** 섹션에서 **웹 아이콘 `</>`** 클릭
3. 앱 이름 입력 (예: `pool-web`) → **앱 등록**
4. 나오는 코드에서 `firebaseConfig` 부분만 복사:

```javascript
// 이 부분을 복사하세요
{
  apiKey: "AIzaSy...",
  authDomain: "pool-coupon.firebaseapp.com",
  databaseURL: "https://pool-coupon-default-rtdb.asia-southeast1.firebasedatabase.app",
  projectId: "pool-coupon",
  storageBucket: "pool-coupon.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abcdef"
}
```

### Step 5: 앱에 설정 입력

1. **관리자 앱** (`pool-coupon-admin.html`) 열기
2. Firebase 설정 입력 화면이 나타남
3. 위에서 복사한 설정을 JSON 형태로 붙여넣기:

```json
{
  "apiKey": "AIzaSy...",
  "authDomain": "pool-coupon.firebaseapp.com",
  "databaseURL": "https://pool-coupon-default-rtdb.asia-southeast1.firebasedatabase.app",
  "projectId": "pool-coupon",
  "storageBucket": "pool-coupon.appspot.com",
  "messagingSenderId": "123456789",
  "appId": "1:123456789:web:abcdef"
}
```

4. **"연결하기"** 클릭 → 상단에 🟢 **실시간 연동** 배지 확인
5. **회원 앱** (`pool-coupon-member.html`)도 동일한 설정 입력

> 💡 설정은 각 기기 브라우저에 저장되므로 한 번만 입력하면 됩니다.

---

## 사용 방법

### 관리자 (현장 태블릿)

1. `pool-coupon-admin.html`을 태블릿 브라우저로 열기
2. **쿠폰 사용 탭**: 회원 이름 옆 🎫 사용 버튼으로 차감
3. **이용 내역 탭**: 월별 조회, 정렬(6종), CSV 내보내기
4. **회원 관리 탭 🔒**: 비밀번호 `2602` 입력 후
   - 새 회원 등록 + 초기 쿠폰 설정
   - 기존 회원 쿠폰 추가 구매
   - 회원 삭제

### 회원 (개인 폰)

1. `pool-coupon-member.html`을 폰 브라우저로 열기
2. 최초 1회 Firebase 설정 입력 (관리자에게 받기)
3. 이름 목록에서 **내 이름 터치**
4. 실시간으로 확인 가능:
   - 잔여 쿠폰 수
   - 총 구매 / 사용 현황
   - 월별 사용 내역

> 💡 관리자가 쿠폰을 사용 처리하면 회원 앱에 **즉시 반영**됩니다.

---

## 회원에게 설정 공유하는 방법

Firebase 설정 JSON을 회원에게 전달해야 합니다.

### 방법 1: 카카오톡/메일로 전달
관리자가 설정 JSON을 복사해서 단체톡에 공유

### 방법 2: QR 코드
설정 JSON이 포함된 메모 링크를 QR 코드로 만들어 수영장에 비치

### 방법 3: HTML 파일에 직접 삽입
회원용 HTML 파일의 설정 부분에 미리 값을 넣어서 배포하면,
회원은 파일만 열면 바로 연결됩니다.

---

## 알림 기능

| 잔여 | 알림 |
|:----:|------|
| 2장 | ⚠️ 노란색 경고 팝업 (1회) |
| 1장 | 🔴 빨간색 "마지막 1장" 팝업 (1회) |
| 0장 | 🚨 소진 알림 팝업 |

쿠폰 추가 구매 시 알림 플래그 자동 리셋

---

## 구매 가능 쿠폰 단위

| 5장 | 10장 | 20장 | 30장 | 50장 |
|:---:|:----:|:----:|:----:|:----:|

---

## 배포 옵션

| 방법 | 특징 |
|------|------|
| 파일 직접 열기 | 가장 간단, 인터넷 필요 (Firebase 연동) |
| GitHub Pages | 무료, URL 공유 가능, 추천 |
| Netlify Drop | 드래그&드롭 즉시 배포 |

> ⚠️ Firebase 연동 버전은 인터넷이 필요합니다.
> 오프라인 전용이 필요하면 이전 버전 (`pool-coupon.html`)을 사용하세요.

---

## 기술 스펙

- 단일 HTML 파일 × 2 (관리자 + 회원)
- Firebase Realtime Database (무료 Spark 플랜)
- Vanilla JavaScript, 내장 CSS
- Noto Sans KR + Outfit (Google Fonts)
- Firebase SDK v10 (CDN)

### Firebase 무료 플랜 한도

| 항목 | 한도 |
|------|------|
| 동시 연결 | 100개 |
| 저장 용량 | 1GB |
| 다운로드 | 10GB/월 |

수영장 클럽 규모에서는 무료 플랜으로 충분합니다.

---

## 문제 해결

| 증상 | 해결 |
|------|------|
| "오프라인" 배지가 계속 뜸 | Firebase 설정의 `databaseURL` 확인 |
| 회원 앱에 데이터가 안 뜸 | 관리자/회원 앱의 Firebase 설정이 동일한지 확인 |
| 30일 후 작동 중단 | Firebase Console에서 보안 규칙 업데이트 필요 |
| 설정 JSON 에러 | 키와 값 모두 쌍따옴표(`"`)로 감싸야 함 |
