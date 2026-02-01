---
name: translation-refinement
description: >
  This skill should be used when the user asks to "translate a document",
  "translate to Japanese", "translate to English", "translate to Korean",
  "review translation quality", "check translation naturalness",
  "check if it reads like one author wrote it", or when translating
  technical content and iteratively refining it for naturalness and consistency.
---

# Document Translation and Iterative Refinement

A structured workflow for translating technical documents holistically and refining them through recursive review passes until the output reads as if originally written in the target language by a single author.

## Core Principles

- **Translate by context, not by sentence.** Read the full source, internalize the narrative, then rewrite in the target language. Never map sentences or words one-to-one.
- **Recursive refinement.** Review in multiple passes with different lenses. Stop only when a full pass produces zero issues.
- **Single-author voice.** The final document must read as if one person wrote it in one sitting — uniform terminology, tone, and expression patterns throughout.

---

## Phase 1: Translation

**Actions:**
1. Read the entire source document to internalize the narrative arc and tone
2. Translate section by section, preserving the argument structure
3. Adapt idioms and cultural references for the target audience
4. Keep all code blocks, URLs, and technical terms unchanged
5. Preserve the original markdown structure

**Critical rule:** After translating each section, re-read it in isolation. If any sentence reveals the source language's structure, rewrite that sentence from scratch in the target language.

---

## Phase 2: Naturalness Review (Recursive)

Perform multiple review passes until zero issues are found in a complete pass.

**Each pass scans for:**
- Unnatural sentence structures carried over from the source language
- Punctuation patterns foreign to the target language (e.g., excessive colons)
- Overly long sentences that should be split
- Repeated words or phrases within close proximity
- Expressions that sound translated rather than native
- Section titles that don't match the body's tone

**Process:**
1. Read the full document, list all issues found
2. Apply all fixes
3. Re-read the full document from the beginning
4. If any new issues found → repeat from step 2
5. Stop only when a complete pass produces zero issues

Typically 2–3 passes are needed.

---

## Phase 3: Single-Author Consistency Check

Verify the document reads as if written by one person.

**Check targets:**
- **Quote introduction patterns** — All quote-introducing sentences should use the same ending style (all colons or all periods, not mixed)
- **Terminology** — The same concept uses the same word throughout (e.g., pick one of "alternative pattern" / "alternative method" / "alternative approach", not all three)
- **Register** — No mixing of formal and informal styles (e.g., です/ます vs だ/である in Japanese)
- **Section title tone** — All titles share the same stylistic approach
- **Recurring expressions** — Verify synonyms aren't mixed for the same meaning (e.g., 独自に vs 自ら)

Fix inconsistencies and re-scan until uniform.

> Refer to `references/translation-review.md` for language-pair-specific guidance and the full review checklist.

---

## Additional Resources

### Reference Files

- **`references/translation-review.md`** — Comprehensive review checklist covering naturalness, single-author consistency, technical accuracy preservation, and language-pair-specific common issues
