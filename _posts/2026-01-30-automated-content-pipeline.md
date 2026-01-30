---
layout: post
title: "ADR-0003: Automated Content Pipeline for Dual-View Archives"
date: 2026-01-30
categories: [DevOps, Automation, Career-Engineering]
tags: [Python, CI/CD, Architecture, Productivity]
status: Accepted
decision_makers: ktaekim
description: "Designing a publishing pipeline that separates private career strategies from public technical articles using a single source of truth."
---

## 1. Context & Problem

As I prepare for a career transition to the German market, I maintain two distinct types of documentation:
1.  **Public Persona (The Blog):** Polished technical articles, architectural insights, and portfolio pieces.
2.  **Private Strategy (The Archive):** Raw drafts, interview scripts, English vocabulary practice, and sensitive career strategies.

**The Conflict:**
Managing these in two separate places (e.g., a private Notion for drafts and a public GitHub repo for the blog) creates a **Synchronization Hell**.
*   If I update a technical concept in my private notes, the blog becomes outdated.
*   If I write directly on the blog, I lose the safe space to practice "ugly" drafts or store sensitive interview prep materials.

I needed a system that supports **"One Source, Multi-View"**: A single Markdown file that serves as both my private study guide and my public portfolio.

---

## 2. Options Considered

### Option A: Two Separate Repositories (Manual Sync)
- Keep `career-archive` (Private) and `tech-blog` (Public) completely separate.
- **Pros:** Clear separation of concerns. Zero risk of accidental leaks.
- **Cons:** Violation of DRY (Don't Repeat Yourself). High friction to publish. I would likely stop updating one of them.

### Option B: Public Repository with Hidden Folders
- Use a single public repo but put private notes in a `.gitignore` folder.
- **Pros:** Easy file management.
- **Cons:** Limits the ability to have "Private Metadata" attached *directly* to the public article (e.g., an interview pitch *about* that specific article).

### Option C: The "Filtered Publishing" Pipeline
- Write everything in the Private Repo (`career-archive`).
- Use a delimiter (e.g., `<!-- === END OF PUBLIC CONTENT === -->`) to separate public and private sections within the same file.
- Use a script to parse, filter, and push only the top section to the Public Repo.
- **Pros:** Perfect context locality. I can see my English practice notes right below my technical article. Automated publishing.
- **Cons:** Requires building a custom pipeline. Risk of leaking if the delimiter is typoed (mitigated by strict parsing).

---

## 3. Decision & Rationale

I chose **Option C: The "Filtered Publishing" Pipeline**.

### The Architecture
I treat my career archive as the **"Upstream Source"** and the blog as the **"Downstream Artifact"**.

1.  **Source:** `projects/tech-blog/*.md` in the private repo.
    *   Contains full context: Article + Raw Drafts + Interview Scripts.
2.  **Transformation:** A Python script (`scripts/publish_blog.py`) acts as the ETL (Extract, Transform, Load) tool.
    *   **Extract:** Reads the Markdown file.
    *   **Transform:** Splits content by the delimiter. Updates the filename based on the `date` in Front Matter.
    *   **Load:** Overwrites the corresponding file in the public `_posts` directory.
3.  **Destination:** GitHub Pages builds the static site from the filtered content.

### Why this matters
This is an application of **Software Engineering principles to Career Management**:
*   **Separation of Concerns:** The script handles the view logic (what to show), while I focus on the business logic (content).
*   **Automation:** Eliminates the manual labor of "copy-pasting and deleting private parts."
*   **Safety:** The script explicitly looks for the delimiter. If I forget it, I can configure it to fail-safe (or publish all, depending on risk tolerance).

---

## 4. Trade-offs & Risks

### Trade-offs
*   **Complexity:** I now have to maintain a Python script.
    *   *Mitigation:* The script is simple (< 100 lines) and uses standard libraries.
*   **Coupling:** The private repo is now tightly coupled to the folder structure of the public repo.
    *   *Acceptance:* Since I own both, this coupling is manageable.

### Risks
*   **Accidental Leak:** If I make a typo in the delimiter, private notes could be published.
    *   *Mitigation:* The script prints a summary of what is being published. I review the diff before pushing the public repo.

---

## 5. Things to Consider

### 1. Is this over-engineering?
- Some might say, "Just use two files."
- **Counter-argument:** The friction of opening two files prevents me from reviewing my interview notes. By keeping them in one file, I force myself to review the "Interview Pitch" every time I polish the article. **Context switching is the enemy of deep work.**

### 2. Future Extensions
- Could I automate the "Git Push" step as well?
- Could I add an LLM step to automatically generate the "Vocabulary" section from the article content during the pipeline?
