# Agentic Skills for TDD

Test-Driven Development skills for AI coding assistants. Two skills, stack-neutral and static, that make an assistant work the way Kent Beck's *Canon TDD* describes: write a list of the behaviors first, turn one item into one test, make it pass by the simplest change, refactor only as far as the next test needs, repeat until the list is empty.

**Version:** `tdd-skills 1.0` (see `VERSION`). Core Rule numbering is frozen within a major version; stack skills cite rule numbers against it.

## Two tiers: the process here, the mechanics in a stack skill

These skills decide **the process**: what goes on the test list, which item is next, when a test is seen red, what "green" may change, when refactoring happens, when to commit, and when the work is done. They are the same in every language and framework.

**How** a particular stack runs that process — the test runner, the test tiers and what "all previous tests" costs per tier, the compile-red stub convention, test doubles, framework test annotations, where the list lives — is a separate **mechanics skill per stack**, named `<stack>-tdd`:

| Stack | Skill | Where |
| --- | --- | --- |
| Quarkus | `quarkus-tdd` | [agentic-skills-for-quarkus](https://github.com/jeremyrdavis/agentic-skills-for-quarkus) |
| Spring Boot | `spring-tdd` | planned |
| Python stacks | `pytest-tdd`, … | planned |

Install the TDD skills plus the one stack skill your project uses. Each TDD skill points at `<stack>-tdd` with a soft reference: if it is installed the assistant loads it for the mechanics and the stack's worked example; if not, the TDD rules still apply and the assistant uses the project's test runner.

A stack skill must not redefine a TDD rule. It supplies commands, annotations, tier mappings, and worked examples that cite the TDD Core Rules by number — nothing else.

### Architecture-neutral

The loop is the same whether the code follows Domain-Driven Design, REST over Active Record, or anything else. The project's design conventions decide what a test invokes; these skills decide when and in what order. They never reference an architecture, and no architecture skill needs to reference them.

## What this repository contains

Two skills under `skills/`:

- `tdd-test-list` — step 1 of Canon TDD: the list of behaviors, written before any code, walked through ZOMBIES for completeness, ordered simplest-forcing-case first and exceptions last, appended during the loop, and empty when the work is done.
- `tdd-cycle` — steps 2–5: one test at a time, seen red (compile failure first when something is new), expected values derived by reasoning and predicted before every run, the simplest green, a callout when a test passes without ever failing, simplify, a refactoring step with its own hat, a commit at every green.

Each skill is a `SKILL.md` with the same structure: a foundational principle, red flags, when to use, numbered Core Rules, anti-patterns, an excuse/reality table, a quick-reference checklist, and companion skills. `tdd-test-list/references/zombies.md` carries the ZOMBIES heuristic; `tdd-cycle/references/canon-tdd.md` carries the canonical workflow, Beck's mistake list, and what the skill adds beyond canon and why. Each skill also has an `evals/evals.json` whose expectations are those mistakes, negated.

### Modes

`TDD mode: auto` (the default) runs the loop without pausing. `TDD mode: human` pauses after the list, before each green, and before each refactoring, and commits the red step separately so the history shows the test came first. Say "human mode" or "auto mode" at any point to switch. Both modes commit at every green and after each refactoring.

## Install

There is no `claude mcp add-skill` command. Use one of the following.

### Plugin, per project (recommended)

Add the marketplace once, then install into each project that uses TDD. Project scope records the plugin in that project's `.claude/settings.json`, so nothing is loaded in projects that don't use it.

```bash
claude plugin marketplace add jeremyrdavis/agentic-skills-for-tdd
cd <your-project>
claude plugin install tdd-skills@agentic-skills-for-tdd --scope project
```

One-step form from inside a Claude Code session:

```
/plugin install tdd-skills --marketplace jeremyrdavis/agentic-skills-for-tdd
```

Use `--scope local` for a personal, uncommitted install, and `--scope user` only if you want the skills in every project. For a Quarkus project, also install `quarkus-skills` from the Quarkus repository; it carries `quarkus-tdd`.

### Copy, per project (no manifests)

```bash
git clone https://github.com/jeremyrdavis/agentic-skills-for-tdd.git
mkdir -p <your-project>/.claude/skills
cp -r agentic-skills-for-tdd/skills/tdd-* <your-project>/.claude/skills/
```

Claude Code loads `<project>/.claude/skills/<name>/SKILL.md` for that project. Copy both; the skills cross-reference each other.

## How the skills fit together

- `tdd-test-list` tells you **what to test and in what order**.
- `tdd-cycle` tells you **how each test becomes working code**.
- `<stack>-tdd` tells you **how your stack runs the loop**.

For "add cancel to `Order`": `tdd-test-list` produces the `[TEST]` items (a placed order can be cancelled; a fulfilled one cannot; cancelling raises the event; …); `tdd-cycle` takes the first item, sees it red, makes it green, refactors, commits, and moves to the next; `quarkus-tdd` says which tests run on the plain JVM and which need `@QuarkusTest`, and what to run at each step.

## Using the skills with Claude Code

Once installed, the skills fire on their own: each `SKILL.md` has a description listing the situations and code smells it triggers on, and Claude loads the body only when it matches. Ask in terms of the behavior:

- "Add `cancel` to `Order`, test-first."
- "Write the test list for a tennis scorer before we start."
- "What tests am I missing in this file?"
- "Review this diff: was the test written before the code?"

Say "spike" explicitly when you want exploration without the loop; the result is thrown away.

## Using the skills with Codex

Codex can use the same files as process guidance. Point it at the repository or copy `skills/tdd-*/SKILL.md` into its context, state the task as a behavior change, and ask it to write the test list first. The `evals/evals.json` expectation lists double as a review checklist for any assistant's transcript.

## Repository structure

```text
.
├── .claude-plugin/
│   ├── plugin.json
│   └── marketplace.json
├── VERSION
└── skills/
    ├── tdd-cycle/
    │   ├── SKILL.md
    │   ├── evals/evals.json
    │   └── references/canon-tdd.md
    └── tdd-test-list/
        ├── SKILL.md
        ├── evals/evals.json
        └── references/zombies.md
```

## Contributing

A change to a skill follows the existing template. Keep `SKILL.md` stack-neutral; runner commands and framework annotations belong in a `<stack>-tdd` skill. Adding a rule appends to the list — numbering is frozen within a major version because stack skills cite it.

A new stack skill is named `<stack>-tdd`, defers every process decision to these skills, and carries `references/tdd-cycle.md` (one worked loop in that stack) and `references/testing.md` (the stack's test tooling). See `quarkus-tdd` for the shape.

## Credits

- Kent Beck, [Canon TDD](https://newsletter.kentbeck.com/p/canon-tdd) (2023) and *Test-Driven Development: By Example* (2002) — the workflow and the mistake list.
- Martin Fowler, [Test Driven Development](https://martinfowler.com/bliki/TestDrivenDevelopment.html) (rev. 2023) — the test list first, sequencing as a skill, don't skip refactoring.
- James Grenning, [TDD Guided by ZOMBIES](https://blog.wingman-sw.com/tdd-guided-by-zombies) — the completeness heuristic.
- The `tdd` skill in [lexler/rpg-combat-tdd-vs-habit-hooks](https://github.com/lexler/rpg-combat-tdd-vs-habit-hooks) — the starting point: `[TEST]` lists, two-step red, prediction before each run, and the "passed without red" callout, demonstrated over a full kata transcript.
