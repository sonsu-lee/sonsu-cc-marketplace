# Source Verification Checklist

Use this checklist when verifying citations in technical articles.

## Before Writing

- [ ] Identify all official sources for the topic
- [ ] Fetch each source URL and confirm it resolves (not 404)
- [ ] Extract candidate quotes that support claims in the article
- [ ] Record the exact section/heading where each quote appears

## During Writing

For each citation added to the article:

- [ ] Quote is copied verbatim (character-for-character match)
- [ ] Attribution includes source name, section title, and URL
- [ ] The quote actually supports the claim being made (not taken out of context)
- [ ] Surrounding context in the source does not contradict the article's use of the quote

## After Writing

### Factual Claims

- [ ] Every "X was introduced in version Y" claim is verified against release notes
- [ ] Every date mentioned is confirmed against the source
- [ ] Version numbers are correct and consistent
- [ ] Feature names match the official terminology

### Causation Claims

These are the most common source of errors. For every "A caused B" or "A led to B" statement:

- [ ] Confirm A actually preceded B chronologically
- [ ] Confirm there is a documented causal relationship, not just correlation
- [ ] If the relationship is indirect, make the indirection explicit

**Example of a causation error:**
> "Class syntax stabilization reduced the value of parameter properties."

This is wrong because parameter properties work *on top of* class syntax. The real issue is that parameter properties require runtime code transformation, not that classes made them less useful.

### Categorization

- [ ] Items listed under a heading actually belong to that category
- [ ] TypeScript-specific features are not listed under "JavaScript maturation"
- [ ] Runtime-generating syntax is not grouped with type-only syntax

### Logical Flow

- [ ] Each section's conclusion follows from its evidence
- [ ] The article does not make logical leaps without support
- [ ] Counterarguments or limitations are acknowledged where appropriate

## Re-verification

After fixing any issues:

- [ ] Re-read the affected section in full context
- [ ] Confirm the fix does not break the narrative flow
- [ ] Verify adjacent sections still connect logically
