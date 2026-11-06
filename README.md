# vibecoding_hackathon — 발표 연습실

카메라를 켜고 발표하면 AI 교수님이 질문하고, 끝나면 고칠 점을 진단해 주는 발표 연습 웹입니다. 기획은 [기획안.md](기획안.md)를 보세요.

## 실행

```bash
cd server
cp .env.example .env     # .env 에 ANTHROPIC_API_KEY 입력
npm install
npm start                # http://127.0.0.1:3000
```

- **카메라·마이크는 반드시 Chrome에서 `http://localhost:3000`으로 여세요.** `index.html`을 파일로 직접 열거나(`file://`, `data:`) Claude 앱 안의 내장 브라우저에서는 카메라·마이크가 차단되어 켜지지 않습니다.
- `localhost`에서는 한 번 '허용'하면 권한이 기억됩니다. 파일로 열면 매번 다시 묻습니다.
- 키가 없어도 화면은 열리고, 질문·진단은 규칙 기반 대체 문장으로 동작합니다.
- 모델은 기본 `claude-opus-5-5`이며 `.env`의 `CLAUDE_MODEL`로 바꿀 수 있습니다.

## 구조

| 경로 | 설명 |
|---|---|
| `prototype/index.html` | 화면 전체(준비 → 발표 → 질의응답 → 진단) |
| `server/server.mjs` | 정적 파일 + `/api/questions`, `/api/feedback` (Claude API 호출, 키는 서버에만 보관) |
| `ideas_ranked.md` | 아이디어 100선 점수표 |

## 주요 기능

- **발표**: 현실적인 대강당 화면에 발표 자료(PDF·이미지)와 내 카메라가 겹쳐 뜹니다. 교수님은 왼쪽에서 지켜봅니다.
- **발표 대본(선택)**: `.txt` `.md` `.pdf` 대본을 올리면 AI가 슬라이드에 맞게 나눠 자료 아래에 보여 줍니다. 안 올리면 대본 칸은 나오지 않습니다.
- **음성 인식 상태**: 실시간 자막 대신 "잘 되고 있는지"만 알려 줍니다(소리 크기 표시 포함).
- **시선 분석**: 카메라 영상에서 정면/아래/옆을 추정해 대본만 보지 않는지 판단합니다. 영상은 브라우저 안에서만 처리합니다.
- **강의실 반응**: 말 속도·정적·군더더기어에 따라 학생과 교수님이 반응합니다(지루함/버벅임/속사포/완벽). 발표 화면의 "강의실 반응 미리보기" 버튼으로 마이크 없이도 볼 수 있습니다.
- **질의응답·진단**: 교수님 질문에 음성으로 답하고, Claude가 진단합니다.

## 배포 (GitHub Pages + Supabase)

- 화면은 GitHub Pages(`prototype/`), AI는 Supabase Edge Function(`supabase/functions/presentation-ai`)이 맡습니다.
- 화면은 `localhost`에서 열면 내 컴퓨터의 Node 서버를, 그 밖의 주소에서는 Supabase 함수를 호출합니다.
- Claude 키는 Supabase 대시보드 **Edge Functions → Secrets**에 `ANTHROPIC_API_KEY`로 저장합니다(저장소에 올리지 않습니다).
- 허용 주소는 기본 `https://teamjegal.github.io`, `localhost:3000`입니다. 다른 주소를 쓰려면 Secrets에 `ALLOWED_ORIGINS`(쉼표로 구분)를 추가하세요.
- 함수 프롬프트를 고칠 때는 `server/server.mjs`와 `supabase/functions/presentation-ai/index.ts` 두 곳을 함께 고칩니다.
