---
layout: post
title: "ADR-0002: Building an AI-Driven Blogging Pipeline"
date: 2026-01-03
categories: [Automation, AI, Productivity]
tags: [Gemini, Claude, Jekyll, CI/CD]
status: Accepted
decision_makers: ktaekim
description: "How I leveraged AI tools to reduce the friction of technical writing, ensuring sustainability while preserving authentic engineering thoughts."
---

## 1. Context & Problem

I recently experienced a significant productivity boost at work using **Claude Code** and the **Gemini CLI**. It made me realize that AI agents could handle more than just coding—they could manage my entire knowledge pipeline.

I've always struggled to maintain a technical blog.
The reasons are common among engineers:
*   **Perfectionism:** A public post feels like it needs to be flawless (grammar, formatting, fact-checking).
*   **Time Cost:** Writing a high-quality post takes hours, which I'd rather spend coding.
*   **Friction:** The gap between "having an idea" and "publishing" is too wide.

I needed a system that allowed me to dump my raw thoughts and have them refined and published automatically, without losing my original voice or logic.

---

## 2. Options Considered

### Option A: Manual Writing
- The standard approach.
- **Result:** Proven failure. I start enthusiastic but stop after 3 posts due to time constraints.

### Option B: Give Up on Blogging
- Focus only on coding.
- **Result:** No portfolio, no structured archive of my growth.

### Option C: AI-Augmented Pipeline
- **Workflow:** Raw Notes → AI Agent (Refinement) → Git Commit → Jekyll Build.
- **Philosophy:** "AI polishes the form, but I provide the substance."

---

## 3. Decision & Rationale

I chose **Option C: AI-Augmented Pipeline**.

### The Implementation
I established a set of rules (stored in `GEMINI.md` and `CLAUDE.md`) that instruct the AI agent on how to process my drafts.

1.  **Input:** I write a rough draft in Korean or broken English. It contains the core logic, trade-offs, and facts.
2.  **Processing:** The AI agent (Gemini/Claude) reads the draft.
    *   It **fixes grammar and formatting**.
    *   It **structures** the content into an ADR format.
    *   It **never changes the core logic** or invents conclusions I didn't reach.
3.  **Output:** A clean Markdown file ready for Jekyll.

### Why this works
*   **Sustainability:** The effort to publish drops from 4 hours to 30 minutes.
*   **English Practice:** I force myself to review the English output, learning professional expressions from the AI's corrections.
*   **Authenticity:** The "Things to Consider" section forces me to critique my own decisions, ensuring I'm not just passively generating content.

---

## 4. Trade-offs & Risks

### Risks
*   **"AI Slop":** There is a risk that the writing feels generic.
    *   *Mitigation:* I explicitly instruct the agent to preserve my "colloquial" tone where appropriate and avoid flowery "ChatGPT-style" intros.
*   **Over-reliance:** My raw writing skills might atrophy.
    *   *Acceptance:* I trade raw writing speed for **architectural clarity** and **volume of output**.

---

## 5. Things to Consider

### 1. Is this "Vibe Coding" for prose?
- Just as we use AI to generate boilerplate code while we focus on logic, this pipeline generates "boilerplate prose" while I focus on the *engineering narrative*.
- If an interviewer asks, I can proudly say: "I treat my blog like a CI/CD pipeline. I optimized the bottleneck."

### 2. The "Authenticity" Question
- If AI writes the sentences, is it my blog?
- **Answer:** Yes, because the *decisions* and *trade-offs* are mine. The AI is just the editor.
