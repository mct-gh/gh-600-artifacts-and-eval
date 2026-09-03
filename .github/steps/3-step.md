## Step 3: 평가 신호를 자동으로 만든다

에이전트 결과를 무엇으로 평가할까요. 사람의 눈만으로는 속도를 못 따라갑니다.
자동 스캔 도구가 평가 신호를 만들어 줍니다.

<img width="180" alt="Jetpacktocat" src="../images/jetpacktocat.png" />

### 📖 이론: 평가 신호와 품질 게이트

평가는 성공 기준에서 시작합니다. 그리고 그 기준을 시스템이 확인하게 만듭니다.

GitHub 에서 평가 신호가 나오는 곳은 이렇습니다.

| 신호 | 어디서 | 무엇을 말해 주나 |
| --- | --- | --- |
| 상태 체크 | PR 의 Checks 탭 | 빌드와 테스트가 통과했는가 |
| 코드 스캐닝 | Security 탭 | 취약점이 새로 생겼는가 |
| 시크릿 스캐닝 | Security 탭 | 비밀 값이 커밋됐는가 |
| 의존성 검토 | PR | 위험한 의존성이 추가됐는가 |
| 워크플로 아티팩트 | Actions 탭 | 실행 중 무슨 일이 있었나 |

이 신호들을 **필수 체크**로 등록하면 평가가 강제됩니다.
등록하지 않으면 빨간 X 가 떠도 머지가 됩니다.

> [!NOTE]
> 예전에는 GitHub Models 로 프롬프트 평가를 돌릴 수 있었지만
> **2026년 7월 30일자로 완전히 폐지**됐습니다. 플레이그라운드, 모델 카탈로그,
> 추론 API, BYOK 전부입니다. 지금 시험 범위에서 "자동 스캔 도구로 평가 신호를
> 생성한다" 는 코드 스캐닝과 워크플로 아티팩트를 뜻합니다.

### ⌨️ 실습: 코드 스캐닝을 켠다

1. 아래 경로에 파일을 만듭니다.

    ```
    .github/workflows/codeql.yml
    ```

1. 아래 내용을 붙여 넣습니다.

    ```yaml
    name: CodeQL

    on:
      push:
        branches: [ main ]
      pull_request:
        branches: [ main ]
      schedule:
        - cron: "0 3 * * 1"

    jobs:
      analyze:
        runs-on: ubuntu-latest
        permissions:
          contents: read
          security-events: write
          actions: read
        strategy:
          fail-fast: false
          matrix:
            language: [ javascript-typescript ]
        steps:
          - uses: actions/checkout@v4
          - name: Initialize CodeQL
            uses: github/codeql-action/init@v3
            with:
              languages: ${{ matrix.language }}
          - name: Perform CodeQL Analysis
            uses: github/codeql-action/analyze@v3
    ```

1. **Commit changes** 로 `main` 에 커밋합니다.

1. **Security → Code scanning** 에서 결과를 확인합니다.

    분석에 몇 분 걸립니다. 결과가 없어도 정상입니다. 우리 앱은 아주 작습니다.
    중요한 것은 신호를 만드는 장치가 붙었다는 사실입니다.

<details>
<summary>문제가 있나요? 🤷</summary><br/>

- **채점이 통과하지 않습니다**
  - 파일이 `.github/workflows/codeql.yml` 인지 확인하세요.

- **CodeQL 실행이 실패합니다**
  - `security-events: write` 권한이 있는지 확인하세요. 이게 없으면 결과를 올리지 못합니다.
  - 비공개 리포지토리는 플랜에 따라 코드 스캐닝이 제한될 수 있습니다.

- **Security 탭이 안 보입니다**
  - 리포지토리 Settings 에서 코드 스캐닝이 켜져 있는지 확인하세요.

</details>

---
