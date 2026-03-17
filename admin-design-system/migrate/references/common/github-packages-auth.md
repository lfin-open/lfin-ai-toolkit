# GitHub Packages Authentication Setup

Ask user using `AskUserQuestion` tool:

```
질문: "GitHub Packages 인증이 설정되어 있나요? (@l-fin/ui-components 설치에 필요)"
옵션:
- "설정됨" - 이미 .npmrc에 GitHub Packages 인증 설정 완료
- "설정 안됨" - 설정 방법 안내 필요
```

**If user selects "설정 안됨"**, show the following instructions and exit:

````markdown
## 사전 설정 (최초 1회)

@l-fin/ui-components는 GitHub Packages에 호스팅되어 있어 인증 설정이 필요합니다.

### 1. GitHub Personal Access Token 생성

1. GitHub → Settings → Developer settings → Personal access tokens → Tokens (classic)
2. "Generate new token (classic)" 클릭
3. 권한 선택: `read:packages` ✅
4. 토큰 생성 후 복사 (다시 볼 수 없음!)

### 2. 홈 디렉토리에 .npmrc 설정

터미널에서 다음 명령어 실행:

```bash
echo "@l-fin:registry=https://git.lfin.kr/api/packages/l-fin/npm/" >> ~/.npmrc
echo "//git.lfin.kr/api/packages/l-fin/npm/:_authToken=YOUR_PKG_TOKEN" >> ~/.npmrc
```
````

⚠️ `YOUR_PKG_TOKEN`을 생성한 토큰으로 교체하세요.

### 3. 설정 확인

```bash
cat ~/.npmrc
```

다음과 같이 출력되면 성공:

```
@l-fin:registry=https://git.lfin.kr/api/packages/l-fin/npm/
//git.lfin.kr/api/packages/l-fin/npm/:_authToken=your_token_here...
```

설정 완료 후 `/migrate`를 다시 실행해주세요.

**IMPORTANT**: Exit migration if GitHub Packages authentication is not configured.
