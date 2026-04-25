---
layout: post
title: "ADR-0001: Why This Blog Has No Tutorials"
date: 2026-01-03
categories: [Career, Philosophy]
tags: [Blogging, ADR, Thoughts]
status: Accepted
project_id: blog
decision_makers: ktaekim
description: "A decision record on why I chose to prioritize raw thought processes and architectural decisions over polished tutorials for this engineering blog."
---

## 1. Context & Problem

Deciding to start a technical blog is easy; maintaining it is the hard part.
Most engineering blogs focus on **Tutorials**, **How-to Guides**, or **Best Practices**. While valuable, these formats require significant time for verification, screenshotting, and polishing.

My goal is not to become a tech influencer or an educator, but to **archive my architectural decisions and problem-solving processes**.
I needed a format that allows me to:
1.  Capture engineering thoughts with low friction.
2.  Prove my architectural thinking to future employers.
3.  Sustain the writing habit without burnout.

The core question was: *How can I maintain a high-value technical blog with minimal time investment?*

---

## 2. Options Considered

### Option A: The Traditional Tutorial Blog
- **Format:** Step-by-step guides, "How to build X with Y".
- **Pros:** High traffic potential, immediate value to juniors.
- **Cons:** Extremely time-consuming. Requires high accuracy. Focuses on "How" rather than "Why".

### Option B: The "Thought Process" Archive (ADR Style)
- **Format:** Records of context, constraints, trade-offs, and decisions.
- **Pros:** High authenticity. Demonstrates seniority and architectural insight. Low friction (focus on logic, not formatting).
- **Cons:** Lower general traffic. Less "friendly" to beginners.

### Option C: Private Notes (Obsidian/Notion)
- **Format:** Unpolished internal notes.
- **Pros:** Zero friction. No public pressure.
- **Cons:** Zero career leverage. No pressure to structure thoughts logically.

---

## 3. Decision & Rationale

I decided to go with **Option B: The "Thought Process" Archive**.

This blog will serve as a **public repository of my engineering decisions**, not a classroom.
The rationale is as follows:

1.  **Differentiation:** There are enough tutorials on the internet. What is scarce is the **context behind decisions** made by senior engineers.
2.  **Portfolio Value:** For a role like SRE or Software Architect, showing *how I think* is more valuable than showing *what I know*.
3.  **Sustainability:** By using an AI-assisted pipeline (detailed in ADR-0002) to refine my raw thoughts, I can publish consistently without the burden of copy-editing.

---

## 4. Trade-offs & Risks

### Trade-offs
*   **Readability vs. Depth:** I sacrifice the broad appeal of easy-to-read tutorials for the depth of raw engineering reality.
*   **Polished vs. Authentic:** The content might feel "dry" or "dense" compared to typical dev blogs, but it will be authentic to my actual work experience.

### Risks
*   **"Unkind" Perception:** Recruiters might perceive the lack of tutorials as a lack of communication skills.
    *   *Mitigation:* I will ensure that the "Context" sections are written clearly enough to be understood by non-experts.

---

## 5. Things to Consider (Self-Reflection)

### 1. Can I prove "Empathy" without tutorials?
- Tutorials often demonstrate empathy for the reader. Without them, I must ensure my ADRs explain the *context* kindly.
- **Action:** Always double-check if the "Context" section provides enough background for a stranger.

### 2. The "20% Public" Rule
- My work involves sensitive company data. I plan to publish only about 20% of my notes.
- **Risk:** The blog might look sparse.
- **Action:** Generalize architectural patterns enough to be shared without leaking secrets.
