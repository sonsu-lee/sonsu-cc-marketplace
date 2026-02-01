---
name: technical-writing
description: >
  This skill should be used when the user asks to "write a technical article",
  "draft a guide", "write documentation with citations", "write a reference-based post",
  "create a technical blog post", or when producing technical content that requires
  official source citations and factual accuracy review.
---

# Technical Article Writing with Source Citations

A structured workflow for producing technical articles grounded in official sources, built around a narrative arc rather than a feature list.

## Core Principles

- **Never fabricate quotes.** Every citation must be verified against the actual source. If a quote cannot be confirmed, paraphrase with attribution instead.
- **Narrative over list.** Build a coherent story arc. Each section should answer "why" before showing "what."
- **Sources first, prose second.** Collect and verify all evidence before drafting.

---

## Phase 1: Research & Source Gathering

Collect primary sources before writing anything.

**Actions:**
1. Identify all official sources relevant to the topic (docs, RFCs, proposals, release notes)
2. Fetch each source and extract exact quotes that support key claims
3. Record the canonical URL for each quote
4. Verify each quote exists verbatim at the cited URL

**Source hierarchy (highest → lowest authority):**
1. Official documentation
2. Official blog posts and release announcements
3. GitHub repository READMEs and proposals
4. Conference talks and design documents

> Refer to `references/source-verification.md` for the detailed verification checklist.

---

## Phase 2: Article Drafting

**Actions:**
1. Define the central question or thesis the article answers
2. Outline sections that build the argument progressively
3. Draft each section, embedding citations where claims need support
4. Add code examples immediately after the concepts they illustrate
5. Ensure logical transitions between every section

**Writing guidelines:**
- Open with a relatable scenario or common misconception
- Use blockquotes for direct citations with source attribution
- End with practical takeaways, not just a summary
- Avoid hedging when sources are clear; be direct

**Citation format:**
```markdown
> "Exact quote from the source."
>
> — [Source Name: Section](https://url)
```

---

## Phase 3: Factual Accuracy Review

Review every factual claim against sources.

**Review targets:**
- **Causation claims** — Verify A actually caused B, not just correlation
- **Temporal claims** — Confirm dates, version numbers, chronological order
- **Quote accuracy** — Re-fetch sources, compare character-by-character
- **Categorization** — Items grouped under the correct conceptual umbrella
- **Logical flow** — Each section's argument follows from its evidence

Fix issues and re-review affected sections until clean.

> Refer to `references/source-verification.md` for the full checklist, including common causation error patterns.

---

## Additional Resources

### Reference Files

- **`references/source-verification.md`** — Detailed checklist for verifying citations, catching causation errors, and ensuring categorization accuracy
