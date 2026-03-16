# Intent Sharpening

[한국어](README.ko.md)

A Claude Code plugin that sharpens vague planning intentions into clear **Intent Cards** through Socratic questioning.

## How It Works

1. You describe a vague intention ("I want to add AI recommendations")
2. The skill asks targeted Socratic questions (max 2 per round)
3. A separate **Clarity Judge** agent scores your intent on 6 axes (0–100)
4. Repeat until clarity reaches 80+ (default max 20 rounds, configurable)
5. Output a structured **Intent Card**

Core principle: **questions only, no answers.** The skill sharpens *intent* (Why/What/Who), never *implementation* (How).

## Install

```bash
npx skills add cosmos-makers/intent-sharpening
```

## Usage

```
/intent-sharpening:intent-sharpening I want to build a team knowledge sharing tool
```

Or start without an argument to be prompted:

```
/intent-sharpening:intent-sharpening
```

Set max rounds with `--rounds` or just say it naturally:

```
/intent-sharpening:intent-sharpening --rounds 5 Build a dashboard
/intent-sharpening:intent-sharpening Build a dashboard, keep it to 3 rounds
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

## Full Conversation Example

Here's a real session sharpening the intent for a "team context mounting tool":

<details>
<summary>Click to expand — 5 rounds, 52 → 78 points</summary>

```
> /intent-sharpening:intent-sharpening
  I want to provide a tool for agent users to mount and unmount team contexts.

🪨 Intent Sharpening — Start

Raw input: "I want to provide a tool for agent users to mount and unmount team contexts."

Round 1 — First cut
Why do you need this? What's the problem without team context mount/unmount?
What is "team context" exactly? Without it, what specifically can't users do?

> 1. Team context is shared team memory — it contains everything a new member
>    needs: task owners, specs, glossary, code repo locations, wiki links.
>    It's a folder of markdown files.
> 2. Anyone can mount a team context so their agent can leverage team knowledge.
> 3. Without it, users have to search and explore information from scratch every time.

📊 Round 1 Clarity Score: 52/100
▓▓▓▓▓▓▓▓▓▓░░░░░  Who      10/15  "Agent users" — new hires? veterans? too broad
▓▓▓▓▓▓▓▓▓▓▓▓▓▓░░░░░░  Why      14/20  Reduce search cost — no quantitative evidence
▓▓▓▓▓▓▓▓▓▓▓▓░░░░░░░░  What     12/20  Mount/unmount tool — unclear what changes after mounting
▓▓▓▓▓▓▓▓░░░░░░░  Scope     8/15  Components mentioned — boundaries undefined
▓▓▓░░░░░░░░░░░░  Measure   4/15  No success criteria
▓▓▓░░░░░░░░░░░░  Risk      4/15  Almost no risk awareness
Weakest: Measure, Risk

Round 2 — Keep sharpening
How will you know this succeeded? Like "an agent with team context mounted
can do X" — what's the X?
What happens when a context goes stale? If an agent uses outdated info,
whose problem is that?

> Success means: users ask their agent about team concepts and the agent
> finds answers from team context, or even without explicit questions,
> the agent pulls team knowledge when needed during work.
>
> Stale contexts are a sync problem. I think we should compare the latest
> commit head of the team context repo when it's loaded. (I'm thinking
> repo-based management since this needs to be shared across people.)

📊 Round 2 Clarity Score: 72/100
▓▓▓▓▓▓▓▓▓▓▓░░░░  Who      11/15
▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓░░░░  Why      16/20
▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓░░░░░  What     15/20
▓▓▓▓▓▓▓▓▓░░░░░░  Scope     9/15
▓▓▓▓▓▓▓▓▓▓▓▓░░░  Measure  12/15
▓▓▓▓▓▓▓▓▓░░░░░░  Risk      9/15
Weakest: Scope

  ... (rounds 3–4 continue sharpening Scope and Risk) ...

📊 Round 5 (Final) Clarity Score: 78/100

✅ Intent Sharpening Complete — 78/100 (5 rounds)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

## Intent Card

**One-liner**: A tool that lets agent users mount/unmount shared team knowledge
so agents can perform team-specific work immediately.

**Target (Who)**: Organization members using AI agents (enterprise/team/squad scope)

**Motivation (Why)**: Without team context, users waste time searching for info
every time. Mounting gives agents instant access to team knowledge (owners, specs,
glossary, repo locations, wiki links).

**Deliverable (What)**:
- Mount / unmount / list commands
- Edit team context → push to shared repo
- Sync check via repo commit head comparison
- Agents use team knowledge even without explicit questions

**Scope**:
- Sharing scopes: enterprise / team / squad
- Multiple contexts mountable simultaneously (typically one)
- Includes editing + push; repo-based management
- Permissions follow repo access; PR requirement configurable per repo

**Success Criteria**: Teams create their own contexts; users freely mount/unmount
their team's and other squads' contexts to enrich agent conversations.

**Risk**:
- Sync drift → commit head comparison
- Bad edits propagating → PR workflow + repo version control rollback
- Permissions → repo-level access control

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

⚠️ Still weak:
- Measure: vivid success image but no quantitative metrics
- Risk: security-sensitive content, large context performance, onboarding friction unaddressed
```

</details>

## License

MIT
