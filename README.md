# ktaekim's Technical Blog

개인 아키텍처 결정 기록 및 기술 사고 과정 저장소
Personal architecture decision log and technical thought process repository

---

## What This Is

이 블로그는 **튜토리얼을 쓰지 않습니다**.

대신:
- 기술적 의사결정 과정을 기록합니다
- 트레이드오프와 제약사항을 문서화합니다
- 불확실성과 미완성을 허용합니다
- "왜"에 집중하며, "어떻게"는 부차적입니다

This blog **does not write tutorials**.

Instead:
- Records technical decision-making processes
- Documents trade-offs and constraints
- Allows uncertainty and incompleteness
- Focuses on "why," not "how"

---

## Who This Is For

### Primary Audience
- 미래의 나 (Future me)
- 내 사고 방식을 평가하고 싶은 면접관 (Interviewers evaluating my thinking)

### Not For
- 단계별 가이드를 찾는 사람
- 즉각적으로 실행 가능한 솔루션을 원하는 사람
- 높은 완성도의 튜토리얼을 기대하는 사람

---

## Repository Structure

```
.
├── _posts/         # 기술 블로그 포스트 (Technical blog posts)
├── _adr/           # 아키텍처 결정 기록 (Architecture Decision Records)
├── _layouts/       # Jekyll 레이아웃 템플릿
├── assets/         # CSS, JS, 이미지 등
├── scripts/        # 블로그 운영 도구 (Blog operation utilities)
├── _config.yml     # Jekyll 설정
└── GEMINI.md       # AI 작성 지침 (AI writing guidelines)
```

---

## Writing Philosophy

### 2할 퍼블릭, 8할 프라이빗
- 완전 공개가 목적은 아니지만, 최소한의 긴장감을 유지하기 위해 퍼블릭으로 운영
- 포트폴리오이자 사고 정리 도구

### AI 기반 저마찰 발행
- 휘갈겨 쓴 메모 → AI 정리 → 발행
- 글쓰기 부담을 줄이고 콘텐츠 생산에 집중
- 단, AI는 정리만 할 뿐 내용을 발명하지 않음

### 진화하는 문서
- 모든 포스트는 수정 가능
- 과거 결정을 번복해도 괜찮음
- 히스토리 보존이 중요

---

## Key Documents

- **[GEMINI.md](./GEMINI.md)**: AI 작성 원칙 및 블로그 운영 철학
- **[ADR-0001](./_adr/0001-no-tutorial-policy.md)**: 왜 이 블로그는 튜토리얼을 쓰지 않는가
- **[First Post](./_posts/2026-01-03-why-no-tutorials.md)**: 블로그 철학 소개

---

## Tech Stack

- Static Site Generator: Jekyll
- AI Assistant: Gemini (Google)
- Hosting: GitHub Pages
- Language: Korean (primary), English (bilingual)

---

## Contact

- GitHub: [@ktaekim2](https://github.com/ktaekim2)
- Blog: [ktaekim2.github.io](https://ktaekim2.github.io)

---

## License

All content is © 2026 ktaekim.

Feel free to reference or quote with attribution, but please do not wholesale copy.