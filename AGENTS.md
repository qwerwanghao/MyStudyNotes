# Repository Guidelines

## Project Structure & Content Organization
This repository is a Markdown-first personal knowledge base, not a software project. Put quick captures in `收集/`, preferably `收集/Inbox.md`. Daily notes live in `日志/` and use `YYYY-MM-DD.md`. Long-term notes belong under `知识/` with a path pattern like `知识/大类/主题/分组/文件.md`. Reflections, reviews, and plans go to `感悟/`、`总结/`、`展望/`. Reusable note skeletons live in `模板/`, prompts in `提示词/`, and navigation pages in `索引/` such as `索引/工作台.md`.

Store images and attachments in a sibling `资源/` folder near the related note, for example `知识/CS基础/计算机组成原理/资源/`.

## Build, Check, and Local Workflow
There is no build system or automated test suite. Use lightweight checks before finishing edits:

- `rg --files` to inspect the current note tree quickly
- `sed -n '1,80p' 模板/知识笔记模板.md` to confirm the standard note layout
- `git diff --check` to catch trailing spaces and malformed diffs
- `git status --short` to verify that only intended files changed

After changing links, indexes, or workspace settings, preview the result in Obsidian.

## Writing Style & Naming Conventions
Write in Markdown with short paragraphs, explicit headings, and direct lists. Structured knowledge notes should keep YAML frontmatter such as:

```yaml
---
title: 标题
type: knowledge
date: 2026-04-14
status: distilled
---
```

Use existing naming patterns: `2026-04-15.md` for daily notes, `2026年总结.md` and `2026年计划.md` for yearly review or planning, and concise topic names for knowledge notes. Avoid vague names like “笔记” or “总结” when the subject itself is enough.

## Validation Guidelines
Manual review is required. Check that frontmatter fields are complete, internal links still resolve after moves or renames, and new knowledge notes are linked from relevant indexes or parent notes. Mark uncertain claims as `待核实`. Prefer expanding an existing note over creating a near-duplicate file.

## Commit & Pull Request Guidelines
Follow the existing commit style: short imperative messages such as `Add ...`, `Update ...`, or `Rename ...`. Keep one commit focused on one type of content change. For pull requests, list the affected directories, explain any file moves, and include screenshots only when `.obsidian/` views or workspace settings changed. Do not delete raw records in `收集/` or `日志/` unless explicitly requested.
