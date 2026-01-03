---
layout: post
title: "ADR-0001: 왜 이 블로그는 튜토리얼을 쓰지 않는가"
date: 2026-01-03
status: Accepted
decision_makers: ktaekim
---

## 1. Context / Problem

기술 블로그를 시작하려고 한다. 일반적인 기술 블로그는 튜토리얼, How-to 가이드, 베스트 프랙티스 소개 등을 다룬다. 그러나 내가 원하는 것은 누군가를 가르치는 것이 아니라, **내 사고 과정과 의사결정 맥락을 기록**하는 것이다.

문제는:
- 튜토리얼을 쓰려면 완성도가 높아야 하고, 검증된 정보를 제공해야 한다.
- 나는 블로그 작성에 너무 많은 시간을 쓰고 싶지 않다.
- 동시에 최소한의 퍼블릭 기록은 남기고 싶다 (이직용 포트폴리오, 긴장감 유지).

어떤 방향으로 블로그를 운영할 것인가?

---

## 2. Constraints & Assumptions

- **시간 제약**: 블로그 작성에 과도한 시간을 쓸 수 없음
- **주 독자**: 미래의 나, 또는 이직 시 내 사고 방식을 평가할 면접관
- **목적**: 수익 창출이 아니라 사고 정리 및 기술 용어 영문화
- **AI 활용 전제**: 휘갈겨 쓴 메모 → AI 정리 → 발행 구조 사용
- **퍼블릭/프라이빗 비율**: 2할 퍼블릭, 8할 프라이빗 (완전 공개가 목적은 아님)

---

## 3. Options Considered

### Option A: 전통적인 튜토리얼 블로그
- 독자에게 무언가를 가르치는 방식
- 단계별 가이드, 스크린샷, 재현 가능한 예제 제공
- 장점: 트래픽 확보에 유리, 독자에게 즉각적 가치 제공
- 단점: 완성도 요구, 시간 소모 큼, 내 사고 과정보다는 "정답" 제시에 집중

### Option B: 사고 과정 중심 기록 블로그
- 의사결정 맥락, 고민, 트레이드오프를 기록
- 불확실성과 미완성을 허용
- 장점: 저마찰 발행, 나의 사고 방식 증명, 포트폴리오 효과
- 단점: 독자 친화적이지 않음, 트래픽 낮을 가능성

### Option C: 완전 프라이빗 메모 (Notion, Obsidian 등)
- 블로그가 아닌 개인 도구 사용
- 장점: 노출 리스크 없음, 완전한 자유
- 단점: 긴장감 부족, 이직용 포트폴리오로 사용 불가

---

## 4. Decision & Rationale

**Option B: 사고 과정 중심 기록 블로그**를 선택했다.

이유:
1. **목적 부합**: 내가 원하는 것은 "가르침"이 아니라 "기록"이다.
2. **저마찰 발행**: AI로 정리하므로 글 작성 부담이 적다. 튜토리얼은 검증과 완성도 때문에 시간이 많이 걸린다.
3. **포트폴리오 효과**: 이직 시 내가 어떻게 생각하고 문제를 해결하는지 보여줄 수 있다. 튜토리얼은 "나"보다는 "주제"를 보여준다.
4. **긴장감 유지**: 완전 프라이빗이면 대충 쓰게 되지만, 퍼블릭이면 조금 더 조사하고 신경 쓰게 된다.
5. **차별화**: 튜토리얼은 이미 많다. 사고 과정 기록은 드물고, 아키텍트/시니어 엔지니어의 판단 근거를 보고 싶어 하는 사람들에게는 더 유용할 수 있다.

---

## 5. Trade-offs & Risks

### 포기한 것
- **스스로 정제된 글을 쓰는 능력**: AI에 의존하므로 글쓰기 근육이 약해질 수 있음
- **독자 친화성**: 튜토리얼 형식이 아니므로 대중적 트래픽은 기대하기 어려움
- **즉각적 가치 제공**: 독자 입장에서는 "당장 써먹을 수 있는 지식"이 적을 수 있음

### 얻은 것
- **단기간에 많은 콘텐츠 생성**: 휘갈겨 쓴 메모도 발행 가능
- **진짜 내 생각 기록**: 튜토리얼은 "정답"을 쓰지만, 이 방식은 "내가 왜 그렇게 생각했는지"를 쓴다
- **장기적 가치**: 미래의 나, 또는 내 사고를 평가할 사람에게는 튜토리얼보다 더 유용

### 리스크
- **2할 퍼블릭의 애매함**: 완전 공개도 아니고 완전 비공개도 아니므로, 일부 글은 공개 여부를 고민하게 될 수 있음
- **AI 의존도**: AI가 내 의도를 잘못 해석하거나, 내가 생각하지 않은 내용을 추가할 위험 (→ CLAUDE.md로 경계 설정)

---

## 6. Outcome or Open Questions

### 아직 실행 전이므로 열린 질문들:
- AI 정리 과정에서 내 원본 사고가 얼마나 보존될까?
- "2할 퍼블릭"의 기준은 구체적으로 어떻게 정할 것인가? (민감한 회사 정보, 실패한 실험 등)
- 면접관이나 동료가 이 블로그를 읽었을 때, 실제로 "이 사람의 사고 방식"을 이해할 수 있을까?
- 튜토리얼이 아니라는 것을 독자가 오해하지 않도록 어떻게 명시할 것인가?

### 다음 단계:
- 실제 포스트를 몇 개 작성해보고, CLAUDE.md의 원칙이 잘 작동하는지 검증
- ADR-0002: AI 기반 저마찰 블로그 운영 방식 (선택적)

---

## Decision Summary (EN)

- This blog does not write tutorials; it records **thought processes, decision contexts, and trade-offs**.
- Purpose: Not to teach others, but to externalize my architectural thinking for future self and interviewers.
- Rationale: Low-friction publishing via AI refinement, portfolio effect, and maintaining tension through minimal public exposure (20% public, 80% private).
- Trade-off: Sacrificing broad readership and polished writing skills, but gaining high-volume content creation and authentic thinking records.
- Open question: How well will AI preserve original thought? How to define the "20% public" boundary?
