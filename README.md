# 유튜브 요약

긴 유튜브 영상 링크를 넣으면 AI(Claude)가 한 줄 요약, 핵심 3가지, 타임스탬프별 요점을 만들어 주는 웹사이트예요.

## GitHub 워크플로우로 요약하고 사이트에서 보기

사이트 주소: https://mooyooai.github.io/longshots/

**처음 한 번만**: 저장소 Settings → 왼쪽 Pages → Build and deployment의 Source를 **GitHub Actions**로 바꿔 주세요.

**요약하기**
1. 저장소 위쪽 **Actions** 탭 → 왼쪽 **유튜브 요약** → 오른쪽 **Run workflow**
2. 유튜브 링크를 붙여 넣고 초록색 **Run workflow**
3. 1~2분 뒤 사이트를 새로고침하면 결과가 보여요. 실행 기록 화면에서도 요약을 볼 수 있어요.

**자막 API 키 (필수에 가까움)**: GitHub 서버에서는 유튜브가 직접 요청을 로봇으로 보고 막아요. [Supadata](https://supadata.ai)에 무료 가입(월 100회 무료)한 뒤 받은 키를 Settings → Secrets and variables → Actions → New repository secret에 이름 `SUPADATA_API_KEY`로 넣으세요.

API 키가 없으면 자막까지만 가져오고 요청문을 만들어요. 자동 요약을 쓰려면 Settings → Secrets and variables → Actions → New repository secret에 `ANTHROPIC_API_KEY`를 넣으세요.

## GitHub에서 바로 실행해 보기 (Codespaces)

내 컴퓨터에 아무것도 설치하지 않고 GitHub 안에서 실행할 수 있어요.

1. **API 키 등록 (선택)**
   키가 없어도 실행돼요. 그때는 자막까지만 가져오고, 화면의 "요청문 복사"를 눌러 claude.ai에 붙여 넣으면 같은 요약을 볼 수 있어요.
   자동 요약을 쓰려면:
   GitHub 오른쪽 위 프로필 → Settings → Codespaces → Secrets → New secret
   - Name: `ANTHROPIC_API_KEY`
   - Value: console.anthropic.com에서 발급한 키
   - Repository access: 이 저장소 선택
2. 이 저장소 첫 화면에서 초록색 **Code** 버튼 → **Codespaces** 탭 → **Create codespace on main**
3. 준비가 끝나면 아래 터미널에 입력: `npm run dev`
4. 오른쪽 아래에 뜨는 **Open in Browser**를 누르면 웹사이트가 열려요.

다 쓰고 나면 github.com/codespaces 에서 Codespace를 멈추거나 지우세요. 개인 계정은 매달 무료 사용 시간이 있어요.

## 자막 가져오기만 따로 확인하기

```bash
npm run check:transcript -- "https://www.youtube.com/watch?v=영상ID"
```

"성공"이 나오면 그 환경에서 유튜브 자막을 가져올 수 있다는 뜻이에요. "BLOCKED"가 나오면 유튜브가 그 서버를 막은 것이라 다른 자막 수집 방법이 필요해요.

## 설정

| 이름 | 설명 |
| --- | --- |
| `ANTHROPIC_API_KEY` | Claude API 키. 없으면 "요청문 복사" 모드로 동작 |
| `SUPADATA_API_KEY` | 자막 API 키. 있으면 유튜브 대신 Supadata에서 자막을 가져옴 |
| `SUMMARY_MODEL` | 요약 모델. 비워 두면 `claude-haiku-5-5` |

내 컴퓨터에서 실행할 때는 `.env.example`을 `.env.local`로 복사해서 값을 넣으세요.

## 구조

- `app/page.tsx` 화면
- `app/api/summarize/route.ts` 링크를 받아 요약을 돌려주는 서버
- `lib/youtube.ts` 유튜브 자막 가져오기
- `lib/summarize.ts` Claude로 요약하기
- `scripts/summarize-cli.ts` 워크플로우가 실행하는 요약 스크립트
- `.github/workflows/summarize.yml` 요약 실행과 사이트 배포
- `site/` GitHub Pages 사이트 (결과는 `site/results/`)
