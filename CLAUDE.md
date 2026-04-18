# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is an Obsidian vault for personal knowledge management. The repository is structured around low-friction capture followed by AI-assisted organization into long-term knowledge notes.

**Core principle**: Record first, organize later. LLMs help distill raw notes into structured knowledge, but never fabricate opinions or delete original records.

## Directory Structure

```
收集/          # Capture inbox - lowest friction entry point
日志/YYYY/     # Daily logs - one file per day
知识/          # Long-term knowledge notes (evergreen content)
感悟/          # Personal insights, judgments, methodology changes
总结/          # Reviews (weekly, monthly, yearly, stage-based)
展望/          # Plans and future directions
模板/          # Note templates
提示词/        # Prompts for AI-assisted organization
索引/          # Navigation pages
```

## Note Types & Frontmatter

All notes use this frontmatter structure:

```yaml
---
title: Title
type: daily | capture | knowledge | insight | review | plan
date: YYYY-MM-DD
status: raw | distilled | evergreen
tags: []
source: []
related: []
---
```

**Status levels**:
- `raw`: Original records, unorganized content
- `distilled`: Organized but may still be modified
- `evergreen`: Stable long-term knowledge notes

## LLM Maintenance Rules

When helping maintain this repository:

**Allowed**:
- Update/create notes based on `收集/`, `日志/`, and existing notes
- Merge obviously duplicate notes
- Add frontmatter, tags, and related links
- Move items to more appropriate locations

**Not Allowed**:
- Delete original records
- Fabricate conclusions or present uncertain knowledge as fact
- Beautify emotions/opinions into non-existent conclusions
- Large-scale renaming/migration unless explicitly requested

**Key principles**:
1. Preserve original records - don't delete raw input
2. Separate knowledge facts from personal opinions
3. Mark uncertain content as `待核实` (to be verified)
4. One note per topic
5. Use index pages for navigation, not infinite directory nesting

## Knowledge Directory Structure

`知识/` uses: `大类/主题/分组/资源/` (Category/Topic/Group/Resources/)

Don't create intermediate directories like `Content/` or `Media/`. Keep depth under 4 levels.

## Common Workflows

**Daily organization**: Read `提示词/每日整理Prompt.md` and follow it with `LLM维护手册.md` as the authority.

**Weekly review**: Read `提示词/周整理Prompt.md` to consolidate last 7 days of logs.

**When creating notes**: Prefer updating existing notes. Only create new notes when:
- New topic has clear boundaries from existing notes
- Original note is too long to extend
- Upgrading raw records to long-term notes

## Entry Points

- [工作台](索引/工作台.md) - Main navigation dashboard
- [LLM维护手册](LLM维护手册.md) - Complete LLM maintenance guide
- [MyStudyNotes](MyStudyNotes.md) - Repository overview and usage
