---
name: sharpen
description: Sharpen vague intentions into clear intent cards through Socratic questioning with a separate Clarity Judge agent.
---

# Intent Sharpening

Sharpen the fuzzy intention in a planner's head through **Socratic questioning**.

Core principle: **Never give answers. Sharpen with questions.**
Raw stone (vague intention) → Socratic dialogue → Sculpture (clear intention)

**Important: This skill sharpens "intent" only — never "implementation."**
- Sharpens: Why, What (what changes), Who, Scope, Success criteria, Risks
- Does NOT sharpen: How (tech stack, architecture, libraries, implementation)
- If the user brings up implementation → "Let's set that aside. First, why does this need to happen?"

---

## Mechanism

```
┌─────────────────────────────────────────┐
│  User: enters planning intention         │
└──────────────┬──────────────────────────┘
               ▼
        ┌──────────────┐
        │  Round N      │
        │  Socratic     │◄──── AI asks questions
        │  Dialogue     │────► User answers
        └──────┬───────┘
               ▼
        ┌──────────────┐
        │  Clarity      │◄──── Separate agent judges
        │  Judge        │      (ensures objectivity)
        │  (Agent)      │
        └──────┬───────┘
               ▼
          Score < 80?  ──Yes──► Next round
               │
              No
               ▼
        ┌──────────────┐
        │  Done!        │
        │  Intent Card  │────► Final intent card output
        └──────────────┘
```

---

## Execution Steps

### Step 0 — Parse Input

`/intent-sharpening:sharpen [intention]` or `/intent-sharpening:sharpen`

- If an intention is provided, go straight to Step 1
- If no argument: "What are you planning? Throw me a one-liner."

### Step 1 — First Sharpening (Round 1)

Upon receiving the user's raw input, immediately start Socratic questioning.

```
🪨 Intent Sharpening — Start

Raw input: "{user input}"

---

Round 1 — First cut

[1–2 questions]
```

**Question Strategy** (pick the 1–2 most effective per round):

| Axis | Question Pattern | What It Sharpens |
|------|-----------------|-------------------|
| Who | "Who is this for? What are they doing right now?" | Vague target |
| Why | "Why does this need to happen? What goes wrong if it doesn't?" | Vague motivation |
| What | "What specifically changes? Describe it so I can see it." | Vague deliverable |
| Scope | "Is this in or out? Where's the boundary?" | Vague scope |
| Risk | "What happens if this fails? What's the biggest risk?" | Unrecognized risk |
| Priority | "Why does this come before everything else?" | Missing prioritization rationale |
| Measure | "How will you know it worked? Give me a number." | Missing success criteria |
| Anti | "What is this NOT? What do people commonly misunderstand?" | Preventing misinterpretation |

**Question Rules**:
- **Max 2 questions** per round
- Sharpen the most vague axis first
- Skip axes the user has already answered clearly
- Ask "why?" at least once

### Step 2 — Clarity Judgment

After the user answers, invoke a **separate agent** to judge clarity based on the conversation so far. Separating the judge from the questioner ensures objectivity.

**Prompt sent to the agent**:

```
You are a judge evaluating how clear a planning intention is.
Rate the clarity of the planner's intention on a 0–100 scale based on the conversation below.

Important: Do NOT evaluate "implementation (How)". Tech stack, architecture, UI, etc. do not affect the score. Judge only the clarity of intent.

## Scoring Rubric (max points per item)

| Item | Max | Criteria |
|------|-----|----------|
| Target (Who) | 15 | The intended audience is specified |
| Motivation (Why) | 20 | There is a grounded reason for doing this |
| Deliverable (What) | 20 | A concrete output or change is visible |
| Scope | 15 | Inclusions and exclusions are distinguished |
| Success Criteria (Measure) | 15 | There is a verifiable success criterion |
| Risk / Constraints | 15 | Key risks or constraints are acknowledged |

## Conversation

{Full conversation so far: raw input + each round's Q&A}

## Output Format (JSON)

{
  "score": 75,
  "breakdown": {
    "who": { "score": 12, "max": 15, "note": "..." },
    "why": { "score": 18, "max": 20, "note": "..." },
    "what": { "score": 15, "max": 20, "note": "..." },
    "scope": { "score": 10, "max": 15, "note": "..." },
    "measure": { "score": 8, "max": 15, "note": "..." },
    "risk": { "score": 12, "max": 15, "note": "..." }
  },
  "weakest": "measure",
  "suggestion": "Defining success criteria with numbers would improve the score"
}
```

**Fallback**: If the Agent tool is unavailable in the current environment, the questioner performs the judgment directly. In this case, be extra rigorous — score conservatively and avoid inflating scores for your own questions.

**Handling the result**:
- **80 or above** → Go to Step 4 (Done)
- **Below 80** → Share the result with the user and go to Step 3

### Step 3 — Next Round

Show the judgment result to the user:

```
📊 Round {N} Clarity Score: {score}/100

{breakdown visualization — bar chart style}
▓▓▓▓▓▓▓▓▓▓▓▓░░░  Who      12/15
▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓░░  Why      18/20
▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓░░░░░  What     15/20
▓▓▓▓▓▓▓▓▓▓░░░░░  Scope    10/15
▓▓▓▓▓▓▓▓░░░░░░░  Measure   8/15
▓▓▓▓▓▓▓▓▓▓▓▓░░░  Risk     12/15

Weakest axis: {weakest}
→ {suggestion}

---

Round {N+1} — Keep sharpening

[1–2 questions targeting the weakest axis]
```

Then return to Step 2 (loop).

**Max rounds**: 5. If still below 80 after 5 rounds, force-complete with the current state and explicitly note which axes remain weak.

### Step 4 — Done: Generate Intent Card

Once the score reaches 80+ (or after 5 rounds), generate the final **Intent Card**.

```
✅ Intent Sharpening Complete — {score}/100 ({rounds} rounds)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

## Intent Card

**One-liner**: {intention in one sentence}

**Target (Who)**: {who is this for}
**Motivation (Why)**: {why this needs to happen}
**Deliverable (What)**: {what specifically changes}
**Scope**: {inclusion/exclusion boundaries}
**Success Criteria (Measure)**: {verifiable criteria}
**Risk**: {key risks and mitigations}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{If below 80 after 5 rounds}
⚠️ Still weak: {weakest axes} — address these before execution
```

### Step 5 — Save & Connect

After showing the Intent Card:

> "Want me to save this card? Where should it go?"

**When saving**:
- File path: user-specified location (default: current working directory)
- Filename: `{YYYY-MM-DD} {topic summary} Intent Card.md`
- Frontmatter: `tags: [intent-card]`, `clarity: {score}`, `rounds: {N}`

---

## Guidelines

### Do

- **Sharpen only with questions**: Never suggest answers or paraphrase the user's intent for them
- **Weakest axis first**: Prioritize the `weakest` from the judgment result
- **Respect user language**: Preserve the user's own words. Don't "translate" them
- **Judge every round**: Always use a separate agent for judgment (the questioner must not self-score)

### Don't

- Suggest answers or lead with "Do you mean...?"
- Ask more than 2 questions per round
- Manipulate scores or judge without the agent
- Give empty praise ("Great idea!")
- **Ask implementation (How) questions**: Tech stack, libraries, architecture, UI design — "how to build it" is never asked or sharpened. Implementation decisions come after the intent is clear.

### Edge Cases

- **User doesn't know the answer**: "I don't know" is a valid answer. Recognizing what you don't know is itself clarification.
  → "That's fine. Then this is something to figure out before execution. Let's move on."
- **Already clear intent**: If Round 1 scores 80+, skip unnecessary questions and complete immediately.
- **User requests to stop**: "Stop sharpening" → Immediately generate the Intent Card with the current state (force-complete).

---

## Example

```
User: /intent-sharpening:sharpen I want to add an AI recommendation feature to our service

🪨 Intent Sharpening — Start
Raw input: "I want to add an AI recommendation feature to our service"

Round 1 — First cut
Why do you need this? What's the problem without recommendations?
You said "AI recommendation" — recommending what exactly? Content? Products? People?

User: Our bounce rate is high... we want to recommend related content

📊 Round 1 Clarity Score: 45/100
▓▓▓▓▓░░░░░░░░░░  Who       5/15
▓▓▓▓▓▓▓▓▓▓░░░░░░░░░░  Why      10/20
▓▓▓▓▓▓▓▓░░░░░░░░░░░░  What      8/20
▓▓▓▓░░░░░░░░░░░  Scope     4/15
▓▓▓▓▓▓▓▓░░░░░░░  Measure    8/15
▓▓▓▓▓▓▓▓▓▓░░░░░  Risk     10/15
Weakest axis: scope
→ Need to narrow down where and how recommendations appear

Round 2 — Keep sharpening
Where do recommendations show up? Content detail page? Feed? Everywhere?
You mentioned high bounce rate — which page are users bouncing from?

...
```

---

**Like sculpting stone, sharpen intent with questions. The sharper it is, the faster execution becomes.**
