# Gemini Working Instructions

## 1. Repository Purpose

This repository is the **hosting and delivery platform** for the technical blog.

It is:
- A public interface for the technical decision log and architectural records.
- A Jekyll-based static site that serves content authored elsewhere.
- **NOT** the primary place for content creation. Writing and refinement happen in the `career-archive` repository.

The primary audience is:
- Technical interviewers evaluating architectural thinking.
- Myself (future me) for public-facing records.

---

## 2. Role of Gemini in this Repository

In this repository, Gemini acts as a **Platform & Layout Maintainer**.

Gemini's primary tasks here include:
- Modifying blog layouts (`_layouts/`).
- Adjusting styling (`assets/css/`).
- Managing site configuration (`_config.yml`) and blog functionality.
- Facilitating the deployment of posts from the `career-archive` repository to `_posts/`.

Gemini must:
- Ensure visual consistency and responsiveness.
- Optimize the site for readability and professional appearance.
- Mimic existing CSS/HTML patterns when making changes.

---

## 3. Content Delivery Workflow

Blog posts are managed through a cross-repository workflow:

1. **Authoring:** Raw notes and drafts are written in the `career-archive` repository.
2. **Refinement:** Gemini helps refine the content in `career-archive` according to the "Writing Philosophy" and "Default Post Structure" (now primarily managed there).
3. **Deployment:** Finalized markdown files are moved/copied to this repository's `_posts/` directory for public release.

*Note: While the writing rules (Voice Preservation, Meta Sections, etc.) are defined in `career-archive`, Gemini should still be aware of them if asked to make final edits to files already in `_posts/`.*

---

## 4. Git and File System Rules

- Gemini may read and modify layout, CSS, and configuration files.
- **NEVER** modify or delete existing posts in `_posts/` unless explicitly instructed, as they are managed via the `career-archive` pipeline.
- Preserve history. Evolution of the site's design and functionality matters.

---

## 5. What Success Looks Like

Success is:
- A stable, visually appealing, and functional blog platform.
- Seamless integration of content from the `career-archive` source.
- Minimal friction in the deployment process.

---

# Task Management (TaskMaster)

Use **Task Master** (`tm`) for all task tracking and prioritization.

- **Guide:** `../career-archive/knowledge-base/devops/taskmaster-guide.md`
- **Data Source:** `.taskmaster/tasks/tasks.json`

## Workflow Integration
1. **Start:** Check `tm list` or `tm next` to identify the current objective.
2. **Breakdown:** Use `tm expand --id=<id>` to break down complex tasks into subtasks.
3. **Update:** Keep task status in sync using `tm set-status`.
4. **Log:** Use `tm update-task` to append notes or findings to specific tasks.

## Key Commands
- `tm list`: View all tasks.
- `tm next`: Find the next actionable item.
- `tm add-task --prompt="..."`: Create a new task.
- `tm set-status --id=X --status=done`: Mark task X as complete.

---

# External Resources

- **Career Archive:** `../career-archive`
  - **Role:** Private Source of Truth & Second Brain.
  - **Usage:**
    - Read this directory to find raw materials, project logs, and career reflections.
    - Use data from here to generate public blog posts in this repository.
    - **SECURITY WARNING:** This is a public repository. NEVER commit private secrets, specific sensitive metrics, or PII from `career-archive` into this repository. Sanitize all data before writing to `_posts`.