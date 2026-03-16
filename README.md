# Intent Sharpening

[한국어](README.ko.md)

A Claude Code plugin that sharpens vague planning intentions into clear **Intent Cards** through Socratic questioning.

## How It Works

1. You describe a vague intention ("I want to add AI recommendations")
2. The skill asks targeted Socratic questions (max 2 per round)
3. A separate **Clarity Judge** agent scores your intent on 6 axes (0–100)
4. Repeat until clarity reaches 80+ (max 5 rounds)
5. Output a structured **Intent Card**

Core principle: **questions only, no answers.** The skill sharpens *intent* (Why/What/Who), never *implementation* (How).

## Install

```bash
claude plugin install cosmos-makers/intent-sharpening
```

## Usage

```
/intent-sharpening:sharpen I want to add an AI recommendation feature
```

Or start without an argument to be prompted:

```
/intent-sharpening:sharpen
```

## Scoring Rubric

| Axis | Max | What It Measures |
|------|-----|------------------|
| Target (Who) | 15 | Is the audience specified? |
| Motivation (Why) | 20 | Is there a grounded reason? |
| Deliverable (What) | 20 | Is a concrete output visible? |
| Scope | 15 | Are inclusions/exclusions clear? |
| Success Criteria | 15 | Is there a verifiable measure? |
| Risk / Constraints | 15 | Are key risks acknowledged? |

**Total: 100 points. 80+ = clear enough to execute.**

## Output: Intent Card

```
✅ Intent Sharpening Complete — 85/100 (3 rounds)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

## Intent Card

**One-liner**: Reduce content page bounce rate by showing personalized related content recommendations

**Target (Who)**: Users landing on content detail pages from search/social
**Motivation (Why)**: 68% bounce rate on content pages; users leave without exploring
**Deliverable (What)**: "Related content" section at the bottom of content pages, personalized by viewing history
**Scope**: Content detail pages only; excludes feed, search results, and homepage
**Success Criteria**: Bounce rate drops from 68% to under 55% within 4 weeks
**Risk**: Cold-start problem for new users with no history → fallback to popularity-based recommendations

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

## License

MIT
