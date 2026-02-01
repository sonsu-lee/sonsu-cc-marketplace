# Translation Review Checklist

Use this checklist when reviewing translations of technical articles. The goal is a document that reads as if originally written in the target language by a single native author.

## Pre-Review Setup

Before starting any review pass, read the entire translated document once without marking issues. Build a mental model of the document's tone, rhythm, and voice.

## Pass 1: Naturalness

Scan for artifacts of translation — expressions that reveal the source language.

### Sentence Structure
- [ ] No source-language word order carried into the target language
- [ ] Relative clauses restructured to match target language conventions
- [ ] Passive/active voice adjusted to target language preference
- [ ] Sentence length appropriate for the target language (split or merge as needed)

### Vocabulary
- [ ] Technical terms use the accepted target-language convention (e.g., "ツリーシェイキング" not "木の揺れ")
- [ ] Non-technical words use natural target-language equivalents, not direct translations
- [ ] No "translationese" — expressions that are grammatically correct but sound foreign

### Punctuation and Formatting
- [ ] Colon usage follows target-language conventions
- [ ] Quote introduction sentences end consistently (all colons OR all periods)
- [ ] List item formatting matches target-language style
- [ ] Parenthetical explanations read naturally

### Connectors and Flow
- [ ] Transitional phrases between sections are natural
- [ ] Cause-effect connectors match target-language patterns
- [ ] Paragraph-opening expressions are varied (not all starting the same way)

## Pass 2: Single-Author Voice

Verify the document sounds like one person wrote it.

### Terminology Consistency
- [ ] The same concept uses the same word throughout the document
  - Example: Pick one of 代替パターン/代替方法/代替手段, not all three
  - Example: Pick one of 独自に/自ら for "on its own", not both
- [ ] Technical terms are consistent (don't alternate between translated and transliterated forms)
- [ ] Section title style is uniform

### Register Consistency
- [ ] Formal/informal tone is uniform throughout
- [ ] No mixing of polite forms (です/ます) with plain forms (だ/である) in Japanese
- [ ] Sentence ending patterns are consistent within sections

### Expression Patterns
- [ ] Quote introductions follow a single pattern
- [ ] Emphasis markers (bold, quotes) are used consistently
- [ ] Explanatory asides follow a uniform style

## Pass 3: Technical Accuracy Preservation

Verify the translation preserved all technical content correctly.

### Code and References
- [ ] All code blocks are identical to the source (not translated)
- [ ] URLs are unchanged
- [ ] Version numbers are correct
- [ ] Technical terms in code context are not translated

### Meaning Preservation
- [ ] Nuances in the original are not lost (e.g., "few" vs "some")
- [ ] Hedging language is preserved where the original hedges
- [ ] Strong claims remain strong; soft claims remain soft
- [ ] The argument structure is identical to the source

## Recursive Review Process

```
while issues_found:
    read full document
    list all issues
    apply fixes
    re-read full document
```

**Stop condition:** A complete read-through produces zero issues.

**Typical pass count:** 2-3 passes for a well-done initial translation, 4-5 for a rough one.

## Common Issues by Language Pair

### Korean → Japanese
- Korean colon-heavy style (：) needs adjustment — Japanese uses colons less frequently
- Korean sentence-final forms (～다, ～이다) map to multiple Japanese options (だ, である, のだ) — pick one style
- Korean topic markers create different sentence structures than Japanese — restructure, don't transliterate

### Korean → English
- Korean's frequent use of passive constructions often needs active voice in English
- Korean compound sentences should often be split into shorter English sentences
- Korean formal register maps to professional English, not academic English

### General
- Preserve paragraph structure but allow sentence-level restructuring
- Titles can be adapted rather than directly translated
- Opening hooks may need cultural adaptation to land effectively
