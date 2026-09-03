## Step 1: 두 갈래로 갈랐다가 다시 모은다

여러 에이전트가 동시에 일하면 결과를 어떻게 합칠까요.
서로 직접 대화하게 두는 것보다 훨씬 안정적인 방법이 있습니다.

<img width="180" alt="Inspectocat" src="../images/inspectocat.png" />

### 📖 이론: 팬아웃과 팬인

작업을 여러 갈래로 나눠 병렬로 돌리고(fan-out), 끝나면 하나로 모으는(fan-in)
패턴입니다. GitHub Actions 에서는 `needs` 로 표현합니다.

```
   spec_analyzer  ─┐
                   ├─→  plan_merger
   risk_reviewer  ─┘
```

`plan_merger` 잡에 `needs: [spec_analyzer, risk_reviewer]` 를 쓰면
두 잡이 모두 끝나야 실행됩니다. 이것이 팬인입니다.

> [!IMPORTANT]
> Microsoft Learn 이 이 패턴을 두고 **"이 패턴은 시험에 나온다"** 고 명시하고 있습니다.
> 오늘 배우는 것 중에 시험 출제가 명시된 유일한 항목입니다.

**아티팩트 기반 조정**도 함께 씁니다. 에이전트끼리 직접 대화시키는 대신
각자 결과를 구조화된 파일로 남기고, 다음 단계가 그 파일을 읽습니다.
추론과 쓰기 사이에 명시적인 경계가 생기고, 검토자가 볼 증거가 남습니다.

**동시성 그룹**도 필요합니다. PR 이 자주 갱신되면 워크플로가 겹쳐 돕니다.

```yaml
concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true
```

> [!WARNING]
> `group` 을 `${{ github.ref }}` 만으로 잡으면 브랜치 전역이 되어 무관한 워크플로까지
> 막습니다. 워크플로 이름과 함께 조합해야 합니다.
> 잡 레벨 동시성이나 매트릭스는 **실행 간에는** 적용되지 않습니다.

### ⌨️ 실습: 팬인 워크플로를 만든다

1. 아래 경로에 파일을 만듭니다.

    ```
    .github/workflows/fan-in.yml
    ```

1. 아래 내용을 붙여 넣습니다.

    ```yaml
    name: Fan-in coordination

    on:
      workflow_dispatch:
      push:
        branches: [ main ]

    permissions:
      contents: read

    concurrency:
      group: ${{ github.workflow }}-${{ github.ref }}
      cancel-in-progress: true

    jobs:
      spec_analyzer:
        runs-on: ubuntu-latest
        steps:
          - uses: actions/checkout@v4
          - name: Analyze the course data
            run: |
              mkdir -p out
              echo '{"agent":"spec_analyzer","courses":5,"note":"구조 점검 완료"}' > out/spec.json
          - uses: actions/upload-artifact@v4
            with:
              name: spec-report
              path: out/spec.json

      risk_reviewer:
        runs-on: ubuntu-latest
        steps:
          - uses: actions/checkout@v4
          - name: Review risk
            run: |
              mkdir -p out
              echo '{"agent":"risk_reviewer","risk":"low","note":"정적 파일만 변경"}' > out/risk.json
          - uses: actions/upload-artifact@v4
            with:
              name: risk-report
              path: out/risk.json

      plan_merger:
        runs-on: ubuntu-latest
        needs: [spec_analyzer, risk_reviewer]
        steps:
          - uses: actions/download-artifact@v4
            with:
              path: incoming
          - name: Merge the two reports
            run: |
              mkdir -p out
              jq -s '{merged: .}' incoming/*/*.json > out/plan.json
              cat out/plan.json
          - uses: actions/upload-artifact@v4
            with:
              name: merged-plan
              path: out/plan.json
    ```

1. **Commit changes** 로 `main` 에 커밋합니다.

<details>
<summary>문제가 있나요? 🤷</summary><br/>

- **채점이 통과하지 않습니다**
  - 파일이 `.github/workflows/fan-in.yml` 인지 확인하세요.
  - `needs`, `upload-artifact`, `concurrency` 세 단어가 파일에 있어야 합니다.

- **왜 세 번째 잡에 needs 를 쓰나요**
  - `needs` 없이 두면 세 잡이 전부 동시에 시작합니다.
    앞의 두 결과를 합쳐야 하는 잡은 기다려야 합니다.

</details>

---
