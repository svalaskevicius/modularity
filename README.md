# Modularity Skills

**TL;DR:** A [Pi](https://pi.dev) package for designing and analyzing modular software systems using the [Balanced Coupling](https://coupling.dev) model.

There's no shortage of AI tools that provide code-level feedback: best practices, edge cases, potential bugs. That's useful, but it's not where the costly mistakes hide. In the AI era, code is generated faster than ever, and so technical debt accumulates faster too. Any architectural inefficiency, any misdrawn boundary, any unmanaged coupling will grow into a big ball of mud at a pace that wasn't possible before.

This package operates at the architectural level. It includes two skills:

- **`/skill:modularity-review`** analyzes an existing codebase for coupling imbalances: what knowledge is properly encapsulated, what's leaking across component boundaries, and where cascading changes are waiting to happen. It produces actionable recommendations for improving modularity.
- **`/skill:modularity-design`** goes the other direction, designing modular architectures from functional requirements and producing module design docs with integration contracts, test specifications, and a full coupling assessment.

Both skills are grounded in the [Balanced Coupling](https://coupling.dev) model, so every recommendation traces back to a concrete dimension (integration strength, distance, volatility), not gut feel.

> _"One of the most useful Claude Code skills for your vibe-coded projects so far!"_ — Alexei Ledenev ([pumba](https://github.com/alexei-led/pumba), [k8s-mcp-server](https://github.com/alexei-led/k8s-mcp-server), [ccgram](https://github.com/alexei-led/ccgram))

## Installation

Requires [Pi](https://pi.dev).

Install the package from git (personal scope — available in every project):

```bash
pi install git:github.com/svalaskevicius/modularity@pi
```

Pin a ref, and install it into the current project only (`.pi/settings.json`, loaded after project trust):

```bash
pi install git:github.com/svalaskevicius/modularity@pi --local
```

`@` takes any git ref — a branch (`@main`, `@feature/foo`), a tag (`@v1.5.0`), or a commit (`@e04ba57`). A ref pins the entry, so Pi never refreshes it on its own; move a pinned branch with `pi update git:github.com/svalaskevicius/modularity@pi` (or `pi update --extensions`). Without a ref, you track the default branch and Pi checks for updates at startup.

Then start `pi` in your project and run `/reload` if Pi was already running.

## Skills

Skills are available as `/skill:<name>`. Typing `/skill:` shows the list.

### `/skill:modularity-review` — Modularity Review

Analyzes an existing codebase for coupling imbalances. Use when:

- Reviewing code for coupling problems
- Assessing architecture quality
- Identifying distributed monolith risks
- Investigating why changes in one area break things elsewhere

**How it works:**

1. Reads your code and functional requirements
2. Asks you about domain classification (core/supporting/generic), team structure, and known pain points
3. Maps integrations between components across three dimensions: integration strength, distance, and volatility
4. Applies the balance rule to flag imbalances
5. Produces a review document (Markdown + HTML) with a coupling overview table, issue descriptions, and concrete improvement recommendations

**Usage:**

```
/skill:modularity-review
```

The review output includes hyperlinks to [coupling.dev](https://coupling.dev) for each coupling concept referenced.

### `/skill:modularity-design` — Modular Design

Designs modular architectures from functional requirements. Use when:

- Designing a new system from scratch
- Creating architecture documentation for an existing system
- Producing module-level design specs with integration contracts and test specifications

**How it works:**

1. Reads your functional requirements and asks clarifying questions
2. Classifies domain areas by volatility (core/supporting/generic subdomains)
3. Designs the modular architecture, assessing coupling across all three dimensions
4. Produces design documents for each module (responsibilities, encapsulated knowledge, integration contracts, change vectors)
5. Produces test specifications for each module
6. Produces an architecture overview document
7. Self-reviews the design for modularity imbalances and iterates until clean

Each step requires your approval before proceeding.

**Usage:**

```
/skill:modularity-design path/to/functional-requirements.md
```

Text after the command is passed to the skill as its request; without it, the skill asks for the requirements file.

### Reference skills

Two skills support the two above and are marked `disable-model-invocation: true`, so they never fire on their own. They are loaded by the skills that need them, and remain available as `/skill:modularity-balanced-coupling` and `/skill:modularity-document` if you want them directly:

- **`modularity-balanced-coupling`** — the full model reference (dimensions, balance rule, DDD links)
- **`modularity-document`** — review document structure, coupling.dev link table, and the HTML template

## Output

- Review: `docs/modularity-review/<date>/modularity-review.{md,html}`
- Design: `docs/design/<date>/architecture.md` and `docs/design/<date>/<module>/{design,tests}.md`

`<date>` is the day the skill ran (`YYYY-MM-DD`), resolved by the agent with `date +%Y-%m-%d`.

## Requirements

The skills use only Pi's built-in tools (`read`, `bash`, `edit`, `write`, `grep`, `find`, `ls`) and ask you questions in plain chat — no extra extensions, MCP servers, or dialogs needed. An LSP-backed navigation tool, if present in your session, is used when available to confirm integration points.

## The Balanced Coupling Model

Both skills are grounded in the Balanced Coupling model, which evaluates coupling across three dimensions:

- **Integration Strength** — how much knowledge is shared between components (intrusive > functional > model > contract)
- **Distance** — the socio-technical cost of co-evolving components (code structure, team boundaries, runtime dependencies)
- **Volatility** — the probability of a component needing to change, evaluated from the business domain perspective

The balance rule: `BALANCE = (STRENGTH XOR DISTANCE) OR NOT VOLATILITY`

Coupling is balanced when strength and distance counterbalance each other, or when volatility is low enough to neutralize the imbalance.

Learn more at [coupling.dev](https://coupling.dev).

## Recommended Models

Claude Opus 4.5 or later. The skills rely on nuanced architectural reasoning that benefits from the most capable models.

## Claude Code

The skills follow the Agent Skills specification, so they also work in Claude Code — copy `skills/*` into `~/.claude/skills/` (or `.claude/skills/` in a project) and invoke them by name. The Pi package manifest (`package.json`) is ignored there.

## License

This work is licensed under [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/). For commercial use, contact skill at coupling dot dev.

## AI Training Restriction

This repository and its contents may not be used for training, fine-tuning, or any other form of machine learning model development without explicit written permission from the author.
