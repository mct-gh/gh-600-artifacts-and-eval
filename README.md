# 실습 5 — 팬인 조정과 실패 분석

_두 갈래로 갈랐다가 다시 모으고, 증거를 아티팩트로 남기고, 실패의 원인을 분류합니다._

## 이 실습에 대하여

- **대상**: 에이전트가 여럿일 때 결과를 어떻게 합치고 무엇을 근거로 평가할지 배우려는 분
- **배우는 것**: 팬아웃과 팬인, 동시성 그룹, 아티팩트 기반 조정,
  자동 스캔으로 평가 신호 만들기, 근본 원인 세 갈래 분류
- **만드는 것**: 팬인 워크플로, 아티팩트 세 개, 코드 스캐닝 설정, 실패 분석 문서
- **필요한 것**:
  - GitHub 계정
  - GitHub Actions 사용 가능한 리포지토리 (공개 권장)
- **소요 시간**: 25분

진행 순서

1. 두 잡을 병렬로 돌려 `needs` 로 팬인하는 워크플로를 만든다
2. 실행해서 아티팩트가 실제로 남는지 확인한다
3. 코드 스캐닝을 켜서 평가 신호를 자동으로 만든다
4. 실패 하나를 골라 근본 원인을 세 갈래로 분류한다

> [!TIP]
> 1단계의 팬인 패턴은 Microsoft Learn 이 **"이 패턴은 시험에 나온다"** 고 명시한 항목입니다.
> 오늘 다루는 것 중 출제가 명시된 유일한 패턴이니 특히 주의해서 보세요.

## 시작하는 법

아래 버튼으로 이 실습을 여러분 계정에 복사한 뒤, **20초 정도** 기다렸다가
페이지를 **새로고침** 하세요.

[![](https://img.shields.io/badge/Copy%20Exercise-%E2%86%92-1f883d?style=for-the-badge&logo=github&labelColor=197935)](https://github.com/new?template_owner=mct-gh&template_name=gh-600-artifacts-and-eval&owner=%40me&name=gh-600-artifacts-and-eval&description=GH-600+Lab+C+-+Fan-in+coordination+and+failure+analysis&visibility=public)

<details>
<summary>문제가 있나요? 🤷</summary><br/>

- **공개(public)** 로 만드는 것을 권장합니다. 비공개는 Actions 사용 시간이 차감되고
  플랜에 따라 코드 스캐닝이 제한될 수 있습니다.

20초 뒤에도 준비되지 않으면 [Actions](../../actions) 탭을 확인하세요.

</details>

> [!IMPORTANT]
> **Fork 하지 마세요.** 위 Copy Exercise 버튼을 눌러야 채점이 동작합니다.
> 실습 진행용 워크플로(`0-start-exercise.yml`, `1-step.yml` 등)는 수정하지 마세요.
> 실습에서 새로 만드는 `fan-in.yml` 과 `codeql.yml` 은 괜찮습니다.

## 시험 대응

- **영역 3** — 메모리, 상태 및 실행 관리 (10~15%)
- **영역 4** — 평가, 오류 분석 및 튜닝 수행 (15~20%)
- **영역 5** — 다중 에이전트 조정 오케스트레이션 (15~20%)

## 관련 학습 자료

- [Memory, State, and Evaluation](https://learn.microsoft.com/ko-kr/training/modules/memory-state-evaluation/)
- [Multi-Agent systems and orchestration](https://learn.microsoft.com/ko-kr/training/modules/multi-agent-systems-orchestration/)
- [실습 모음으로 돌아가기](https://github.com/mct-gh/gh-600-labs)
