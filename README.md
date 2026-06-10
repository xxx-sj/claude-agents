# claude-agents

테스트 작성과 리뷰를 **신념 기반으로 일관되게** 수행하는 [Claude Code](https://claude.com/claude-code) 서브에이전트 쌍.

사람의 테스트 기준은 마감이 닥치면 흔들리지만, 에이전트의 시스템 프롬프트는 흔들리지 않는다 — 테스트 품질의 하한선을 코드로 강제하는 것이 이 repo의 목적이다.

| 에이전트 | 역할 | 도구 권한 |
|---|---|---|
| [`test-writer`](./test-writer.md) | 테스트 코드를 직접 작성. `verify`(기존 코드에 테스트 추가) / `fail-first`(TDD·버그 회귀 — RED 확인 후 정지) 2모드 | Read, Edit, Write, Glob, Grep, Bash |
| [`test-reviewer`](./test-reviewer.md) | 기존 테스트 감사 + 부족한 케이스·안티패턴 리포트만. **read-only** | Read, Glob, Grep |

## 핵심 설계

- **9가지 테스트 신념 공유** — 트리아지 먼저(깨지면 가장 아픈 것부터), 행동 검증(구현 결합 금지), mock은 경계에만, 결정성, 진단 가능한 실패, 컨벤션 우선, 상태 검증 우선, Contract 테스트 필수 등. writer는 작성 가이드로, reviewer는 지적 기준으로 같은 신념을 쓴다.
- **부수효과 격리** — reviewer는 수정 도구가 없어 언제든 안전하게 호출할 수 있고, writer만 파일을 만진다.
- **계획 게이트** — writer는 테스트 계획(3버킷 근거 분석)을 먼저 출력하고 턴을 종료한다. 승인 후에만 코드를 작성한다. reviewer 리포트를 그대로 넘기면 게이트를 생략하는 fast-path가 있다.
- **잠재 버그 발견 도구** — 예상 밖 RED/GREEN이 나오면 자동 수정하지 않고 멈춰서 보고한다. "GREEN 나올 때까지 테스트 수정"은 금지.

## 설치

세 `.md` 파일을 `~/.claude/agents/`에 두면 모든 프로젝트에서 자연어로 호출된다 (에이전트 이름을 몰라도 description 키워드 매칭으로 라우팅):

```
goods-order.service.ts에 테스트 추가해줘          → test-writer (verify)
주문 음수 금액 버그, 회귀 테스트 먼저 짜줘         → test-writer (fail-first)
auth.service.spec.ts 테스트 충분한지 봐줘         → test-reviewer
```

## 문서

- [`test-agents-spec.md`](./test-agents-spec.md) — 설계 문서: 목적, 9가지 신념, 모드 결정, 두 에이전트의 상호작용, 디자인 원칙, 변경 이력

## 라이선스

[MIT](./LICENSE)
