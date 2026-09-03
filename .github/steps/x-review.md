## 정리

팬인으로 조정하고, 아티팩트로 증거를 남기고, 스캔으로 평가 신호를 만들고,
실패를 세 갈래로 분류했습니다.

### 꼭 기억할 것

- 팬인은 `needs: [잡1, 잡2]` 로 만든다. **시험에 나온다고 명시된 패턴**이다
- 동시성 그룹은 워크플로 이름과 `github.ref` 를 **함께** 쓴다.
  `github.ref` 만 쓰면 브랜치 전역이 되어 무관한 워크플로까지 막는다
- 잡 레벨 동시성과 매트릭스는 실행 **간**에는 적용되지 않는다
- 에이전트끼리 직접 대화시키기보다 아티팩트로 조정하는 편이 안정적이다
- 증거가 없으면 실패다. 감사할 수 없는 변경은 머지하지 않는다
- 아티팩트 보존 기본 90일. 공개 1~90일, 비공개 최대 400일. 삭제하면 복구 불가
- GitHub Models 는 2026-07-30 폐지. 평가 신호는 코드 스캐닝과 아티팩트로 만든다
- 근본 원인 세 갈래 — **reasoning errors / tool misuse / context issues**
- 고치는 자리 세 곳 — 프롬프트와 지시문 / 메모리와 상태 / 도구 구성
- 같은 체크가 두 번 실패하면 자동 반복을 멈추고 사람에게 넘긴다

### 메모리 세 계층

이 실습에서 아티팩트를 다뤘으니 함께 정리합니다.

| 계층 | 무엇 | 세션을 넘어 남나 |
| --- | --- | --- |
| 단기 | 현재 작업의 작업 컨텍스트 | 아니오 |
| 장기 | 작업 간 재사용되는 정제된 지식 | 예 |
| 외부 | 이슈, PR, 문서, 워크플로 출력 | 예. **진실의 원천** |

정보 종류별로 진실의 원천을 하나로 정합니다.

- 요구사항 → 이슈
- 결정 → PR 또는 문서
- 검증 규칙 → 리포 지시문
- 결과 → 워크플로 로그와 아티팩트

### 시험에서는

- **영역 3** — 메모리, 상태 및 실행 관리 (10~15%)
- **영역 4** — 평가, 오류 분석 및 튜닝 수행 (15~20%)
- **영역 5** — 다중 에이전트 조정 오케스트레이션 (15~20%)

관련 문항 유형
- 로그, 계획, 추적, 출력, 워크플로 아티팩트로 실패를 식별한다
- 근본 원인을 분류한다 (추론 오류, 도구 오용, 컨텍스트나 환경 문제)
- 자동 스캔 도구로 평가 신호를 생성한다
- 병렬 실행을 위한 에이전트 격리를 구성한다

### 더 볼 것

- [Memory, State, and Evaluation](https://learn.microsoft.com/ko-kr/training/modules/memory-state-evaluation/)
- [Multi-Agent systems and orchestration](https://learn.microsoft.com/ko-kr/training/modules/multi-agent-systems-orchestration/)
- [워크플로 동시성 제어](https://docs.github.com/ko/actions/how-tos/write-workflows/choose-when-workflows-run/control-the-concurrency-of-workflows-and-jobs)

### 다음 실습

**[실습 6 — 위험 기반 자율성과 가드레일](https://github.com/mct-gh/gh600-guardrails)**
