# Formatting Standards

Obsidian vault formatting conventions for this project.

---

## Header Hierarchy

**Rules:**
- One H1 (`#`) per note - always the note title
- Use H2 (`##`) for main sections
- Use H3 (`###`) for subsections
- Use H4 (`####`) for sub-subsections (maximum depth)
- Skip heading levels only when semantically necessary
- No trailing punctuation in headers

---

## Wikilink Conventions

**Rules:**
- Use `[[Note Name]]` format (Obsidian auto-completes)
- Use aliases for readability: `[[Long Note Name|shorter text]]`
- Link full note names, not abbreviations
- For section links: `[[Note Name#Section Header]]`
- Prefer explicit links over ambiguous references

---

## List Formatting

**Unordered lists:**
- Use `-` for consistency (not `*` or `+`)
- Two-space indentation for nesting
- Blank line before/after list blocks

**Ordered lists:**
- Use `1.` format (Markdown auto-numbers)
- Two-space indentation for nesting

**Task lists:**
- Use `- [ ]` for incomplete tasks
- Use `- [x]` for completed tasks

---

## Callouts

**Rule:** Use callouts sparingly for emphasis. Specific usage patterns will be defined as needed.

---

## Tagging

**Naming conventions:**
- Lowercase only: `#status/inbox` not `#Status/Inbox`
- Hyphens for multi-word: `#machine-learning` not `#machinelearning`
- No spaces: use hyphens instead
- Maximum 2 levels deep: `#parent/child` (avoid `#parent/child/grandchild`)

**Tag application:**
- Every note requires: 1 status tag + 1 type tag
- Topic tags: 1-5 per note (if >5, topics too granular)
- Use frontmatter `tags: []` array for AI parsing, inline `#tags` for human browsing

**When to create new tags:**
- Status/Type: Never without updating this file first
- Topic tags: Create freely, but review for consolidation when >3 similar tags exist
- Before creating nested tag, ensure parent has >10 notes

**AI-Friendly Practices:**
- Prefer frontmatter array format: `tags: [status/inbox, type/note, programming]`
- Consistent casing enables reliable querying
- Hierarchical namespacing (`status/`, `type/`) prevents collision with topic tags
- Tag names should be searchable keywords (avoid abbreviations)
