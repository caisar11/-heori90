# 허리90 코치

40~50대 직장인 남성을 위한 뱃살(내장지방) 코치 웹앱.
매일 추천 식단(아침·점심 외식·저녁 집밥), 무릎 친화 홈트 루틴, 음식 사진 AI 분석, 체중·허리둘레 기록.

- 단일 HTML 파일, 빌드 없음
- 기록은 각 사용자의 브라우저(localStorage)에 저장, 백업 내려받기/불러오기 지원
- 휴대폰 홈 화면에 추가하면 앱처럼 실행 (PWA)

## 1. GitHub Pages 배포

1. GitHub에서 새 저장소 생성 (예: `heori90`, Public)
2. 이 폴더의 파일을 모두 업로드 (`functions/` 폴더는 올려도 되고 안 올려도 됨)
3. 저장소 **Settings → Pages → Source: Deploy from a branch → main / (root)** 저장
4. 1~2분 뒤 `https://caisar11.github.io/heori90/` 에서 열림

명령어로 올릴 때:
```bash
cd heori90
git init && git add . && git commit -m "허리90 코치 첫 배포"
git branch -M main
git remote add origin https://github.com/caisar11/heori90.git
git push -u origin main
```

## 2. 사진 분석 켜기 (개인 사용)

1. https://console.anthropic.com/settings/keys 에서 API 키 발급 (결제 수단 등록 필요)
2. 앱 → **기록** 탭 → 맨 아래 **AI 설정**에 키 입력 → 저장
3. 키는 그 브라우저에만 저장됨. 저장소 코드에는 절대 키를 넣지 말 것

## 3. 상용화: 서버 경유 모드 (사용자는 키 불필요)

`functions/index.js` 는 Firebase Cloud Functions 프록시입니다.

```bash
npm i -g firebase-tools
firebase login
firebase init functions        # 기존 functions/ 폴더 사용, JavaScript 선택
firebase functions:secrets:set ANTHROPIC_API_KEY
firebase deploy --only functions
```

배포 후 나온 `analyze` 함수 주소를 `index.html` 의 `const PROXY_URL = "";` 에 넣고 다시 push 하면,
모든 사용자가 키 없이 분석을 쓸 수 있습니다. Blaze(종량제) 요금제가 필요합니다.

공개 서비스로 열기 전에 반드시 추가할 것:
- Firebase Authentication 로그인 + 함수에서 ID 토큰 검증
- 무료 사용자 하루 분석 횟수 제한 (Firestore에 카운트)
- 기록을 Firestore로 옮겨 기기 간 동기화
- 결제(토스페이먼츠 등)와 개인정보처리방침·건강정보 별도 동의

## 파일

| 파일 | 역할 |
|---|---|
| `index.html` | 앱 전체 (화면·식단·운동·분석·기록) |
| `manifest.json`, `icon*.png`, `icon.svg` | 홈 화면 설치용 |
| `.nojekyll` | GitHub Pages가 파일을 그대로 서빙하도록 |
| `functions/` | 상용화용 AI 프록시 (선택) |
