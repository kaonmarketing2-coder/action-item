# Agent Manifest (agent.json) 가이드

`agent.json`은 **AI 직원의 인사기록카드**입니다. 통합 안내데스크(오케스트레이터)는
이 파일 하나만 읽고 새 Agent를 알아보고, 어떤 질문을 보낼지 판단하고, 어떻게 호출할지 결정합니다.
새 팀이 Agent를 추가할 때 개발 협의 대신 **이 파일을 작성해 등록**하면 연결이 끝나는 것이 목표입니다.

## 파일 구성

| 파일 | 역할 |
|---|---|
| `manifest.schema.json` | 공통 규격 (모든 agent.json이 지켜야 할 양식) |
| `traffic/agent.json` | 트래픽 담당 AI 작성 예시 |
| `news/agent.json` | 뉴스 담당 AI 작성 예시 |
| `welmate/agent.json` | 웰메이트 담당 AI 작성 예시 (개인정보 취급 Agent 예시) |

## 항목별 의미 — 쉬운 비유

| 항목 | 비유 | 작성 요령 |
|---|---|---|
| `name` / `displayName` | 사번 / 명함 이름 | 내부 ID는 영문, 표시 이름은 한글 |
| `description` | **자기소개서** | 가장 중요. 안내데스크가 이 글을 읽고 질문을 배분하므로, 담당하는 일과 **담당하지 않는 일**까지 구체적으로 |
| `owner` | 소속 팀 / 비상연락망 | 장애·문의가 여기로 라우팅됨 |
| `endpoint` | 내선번호 | 안내데스크가 호출하는 주소 |
| `permissions` | 출입증 등급 | 누가 시킬 수 있는지, 개인정보를 다루는지 |
| `tools` | **직무기술서** (부탁하면 하는 일) | 기능마다 입력(주문서 양식)과 출력(납품 양식)을 고정 |
| `tools[].requiresApproval` | 결재 필요 도장 | 메일 발송·지급 등 밖으로 나가는 일은 `true` |
| `events` | 먼저 보고하는 일 | 이 Agent가 알아서 알리는 알림 목록 |
| `jobs` | 정기 업무 일정표 | 매월/매일 알아서 도는 작업 (문서화 목적) |

## 새 Agent를 추가하는 절차

1. **복사** — 기존 예시 중 가장 비슷한 것을 복사해 시작 (`traffic/agent.json` 추천)
2. **작성** — `description`과 `tools`부터. 도구는 3~5개면 충분하며, 많을수록 좋은 게 아니라 명확할수록 좋음
3. **검증** — 스키마에 맞는지 자동 체크:
   ```bash
   npx ajv-cli validate -s agents/manifest.schema.json -d agents/<이름>/agent.json --spec=draft2020
   ```
4. **등록** — 검증 통과한 파일을 Agent Registry에 등록하면 안내데스크가 다음 갱신 주기에 자동 인식

## 작성 시 자주 하는 실수

- `description`을 한 줄로 쓰는 것 — 안내데스크의 배분 정확도가 떨어짐. "무엇을 담당하고 무엇은 담당하지 않는지"까지 쓸 것
- 도구 출력 형식을 자유 텍스트로 두는 것 — 다른 Agent 결과와 조합할 수 없게 됨. 출력도 반드시 구조화
- 외부 발송 기능에 `requiresApproval`을 빠뜨리는 것 — 등록 심사에서 반려 사유
- 개인정보를 다루면서 `handlesPersonalData: false`로 두는 것 — 마찬가지로 반려 사유
