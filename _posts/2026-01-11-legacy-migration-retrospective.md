---
layout: post
title: "레거시 마이그레이션 회고: 문서 없는 코드를 넘어 아키텍처 고도화로"
date: 2026-01-11 15:00:00 +0900
categories: [Engineering, Migration, Refactoring]
tags: [Legacy Migration, Reverse Engineering, Design Patterns, MSA]
description: "기획서가 유실된 레거시 보안 시스템을 분석하여 MSA 기반의 신규 플랫폼으로 성공적으로 이관한 경험을 공유합니다. Factory 패턴을 활용한 UI 복잡도 해결과 스냅샷 기반 롤백 전략을 중점적으로 다룹니다."
---

## 들어가며: 지도가 없는 탐험

개발자로서 가장 난감한 순간 중 하나는 "기획서나 명세서는 없지만, 현재 돌아가는 시스템과 똑같이 만들어주세요"라는 요청을 받을 때입니다. 최근 6주간 진행했던 **보안 위협 탐지 설정(Threat Detection Setting)** 모듈의 마이그레이션 프로젝트가 정확히 그런 케이스였습니다.

기존의 Monolithic Web 방식(JSP/XML 기반)으로 구축된 레거시 시스템을 Spring Boot 기반의 MSA 환경으로 이관해야 했습니다. 단순한 '복사-붙여넣기'식 이동이 아니라, 모던 웹 프레임워크(Bootstrap 5, ES6+) 위에서 아키텍처를 재설계해야 하는 과제였습니다.

이 글에서는 문서가 없는 상황에서 어떻게 비즈니스 로직을 역설계했는지, 그리고 이 과정에서 적용한 기술적 해결책들에 대해 회고해보고자 합니다.

---

## 1. 난관: 문서의 부재와 숨겨진 로직들

가장 큰 문제는 레거시 시스템의 구체적인 동작 명세가 없다는 점이었습니다. V2 시스템의 소스 코드(Java, XML 쿼리, JSP 내의 파편화된 스크립트)만이 유일한 진실(Source of Truth)이었습니다.

### 역설계(Reverse Engineering) 접근법
코드를 읽는 것이 곧 문서를 읽는 것이었습니다. 특히 다음과 같은 숨겨진 로직들을 찾아내는 데 집중했습니다.
- **암시적 의존성:** DB 스키마에는 드러나지 않는 특정 필드 간의 연결 고리.
- **자동 생성 규칙:** 사용자가 입력하지 않아도 백엔드에서 조용히 생성되는 ID 규칙들.

저는 기존 화면을 띄워놓고 모든 입/출력 값을 캡처한 뒤, 레거시 쿼리 로그와 대조하며 로직을 하나씩 도식화하는 방식으로 '살아있는 문서'를 만들어 나갔습니다.

---

## 2. 해결 전략 1: 복잡한 UI 로직의 구조화 (Factory Pattern)

이번 프로젝트의 프론트엔드 핵심은 **'룰 등록/수정 팝업(Rule Form Modal)'**이었습니다. 하나의 모달이 '신규 등록', '수정', '복사 등록', '중첩 모달' 등 다양한 모드로 동작해야 했습니다. 레거시 코드에서는 수많은 `if-else` 문으로 이를 처리하고 있어 유지보수가 매우 힘들었습니다.

이를 해결하기 위해 **팩토리 패턴(Factory Pattern)**을 도입했습니다.

```javascript
// 개념적 예시
class ModalFactory {
    static create(mode) {
        switch(mode) {
            case 'CREATE': return new CreateModeStrategy();
            case 'EDIT': return new EditModeStrategy();
            case 'COPY': return new CopyModeStrategy();
            default: throw new Error('Unknown mode');
        }
    }
}
```

각 모드별 동작(초기 데이터 로딩, 저장 API 호출 경로, UI 활성화 여부 등)을 별도의 전략 객체로 분리함으로써, 단일 소스 코드(Single Source)를 유지하면서도 확장에 유연한 구조를 만들 수 있었습니다.

---

## 3. 해결 전략 2: 데이터 무결성을 위한 스냅샷 롤백

보안 설정은 잘못 변경되면 시스템 전체의 탐지 성능에 영향을 줄 수 있어 **이력 관리와 롤백(Rollback)**이 매우 중요합니다.

### 문제 상황
단순히 화면에 보이는 데이터만 모아서(`collectFormData`) 저장하는 방식은 위험했습니다. 화면에 보이지 않는(Hidden) 필드나, 복잡한 연관 관계를 가진 리스트 데이터가 누락될 가능성이 있었기 때문입니다.

### 해결책: Server-Side Snapshot
클라이언트가 아닌 **서버 중심의 롤백 전략**을 수립했습니다.
1. 사용자가 롤백을 요청하면, 서버는 DB에 저장된 과거 시점의 전체 데이터(Snapshot)를 로드합니다.
2. 이 원본 데이터를 기준으로 최신 메타데이터(수정자, 수정일시 등)만 갱신하여 새로운 이력으로 저장(`performRollbackSave`)합니다.

이 방식을 통해 프론트엔드에서의 데이터 누락 가능성을 원천 차단하고, 데이터 무결성을 100% 보장할 수 있었습니다.

---

## 4. 성과 및 배운 점

약 6주간의 치열한 마이그레이션 끝에 다음과 같은 성과를 얻었습니다.

- **생산성 향상:** 표준 산정 공수 대비 **120%의 생산성**을 달성하며 프로젝트를 조기 안정화했습니다.
- **코드 품질:** MyBatis의 동적 쿼리와 Batch Insert를 도입하여 레거시의 반복적인 쿼리 성능 문제를 해결했습니다.
- **아키텍처 표준화:** 백엔드(SnakeCase)와 프론트엔드(CamelCase) 간의 데이터 변환 규칙을 `MapWrapper` 수준에서 일원화하여 개발 피로도를 낮췄습니다.

### 마치며
"문서가 없다"는 것은 핑계가 될 수 없었습니다. 오히려 코드를 깊게 파고들며 시스템의 의도를 파악하는 과정에서 더 나은 아키텍처를 고민할 기회를 얻었습니다. 레거시를 걷어내고 더 견고한 시스템을 쌓아올린 이번 경험은 앞으로의 엔지니어링 커리어에 큰 자산이 될 것입니다.

---

{% comment %}
## Meta Section for Career Growth

### 1. Professional English Expressions
- **Source of Truth:** 유일한 진실, 신뢰할 수 있는 원본 데이터. (문서가 없을 때 코드가 유일한 기준이라는 맥락에서 사용)
- **Implicit Dependency:** 암시적 의존성. (명시적으로 선언되지 않았지만 코드 내부 로직에 의해 발생하는 의존 관계)
- **Data Integrity:** 데이터 무결성. (데이터가 전송, 저장되는 과정에서 변형되거나 손상되지 않고 정확함을 유지하는 것)
- **Reverse Engineering:** 역설계. (완성된 시스템을 분석하여 설계 개념과 동작 원리를 파악하는 행위)
- **Architectural Standardization:** 아키텍처 표준화. (시스템 전반에 걸쳐 일관된 설계 패턴과 규칙을 적용하는 것)

### 2. Interview Pitch Script (1-minute)
"In my recent project, I led the migration of a legacy Threat Detection module to a microservices architecture. The biggest challenge was the lack of documentation; the legacy code was the only source of truth. I reverse-engineered the business logic to ensure 100% feature parity.

Technically, I significantly improved maintainability by implementing the Factory Pattern to handle complex UI states, replacing a tangle of legacy if-else logic. I also designed a server-side snapshot rollback mechanism to guarantee data integrity during history restoration. As a result, we achieved 120% productivity compared to the standard estimate and successfully stabilized the core security module."
{% endcomment %}
