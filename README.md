# GameDev Skills

A skill set that turns game dev prompts into outputs you can actually use in a real project.

This repo currently starts with a Phaser 3 + JavaScript skill focused on architecture through design patterns. Simple idea: less fluff, more working gameplay.

## What It Includes Today

- `phaser-design-patterns`: practical use of 22 patterns (creational, structural, and behavioral) for scenes, entities, input, physics, and performance.
- Ready-to-adapt snippets instead of generic `Foo/Bar` examples.
- A complete State Machine example for entity behavior.

## Why Use These Skills

- Helps you pick the right pattern for the actual gameplay problem.
- Reduces coupling across systems (UI, gameplay, audio, input, AI).
- Gives you a clean base to scale features without constant rewrites.
- Includes a runtime validation checklist to avoid leaks and lifecycle issues.

## Quick Start

1. Clone this repository.
2. Make sure the skill is available at `.agents/skills/phaser-design-patterns`.
3. In your agent, invoke the skill by name: `phaser-design-patterns`.
4. Describe your gameplay problem and ask for a pattern-based implementation + snippet + validation steps.

Prompt examples:

```text
Apply the State Pattern for an enemy with Idle, Patrol, and Attack in Phaser 3.
Include the code and how to clean up events when the scene is destroyed.
```

```text
Refactor this spawn system using Factory Method and object pooling.
I want better readability and stable 60 FPS behavior.
```

```text
My UI and gameplay are tightly coupled. Propose Mediator or Observer and give me a minimal implementation.
```

## Repository Structure

```text
.
|-- .agents/
|   `-- skills/
|       `-- phaser-design-patterns/
|           |-- SKILL.md
|           |-- README.md
|           `-- references/
|               |-- patterns-map.md
|               |-- phaser-snippets.md
|               `-- state-machine-pattern-phaser.md
`-- phaser-design-patterns.skill
```

## Featured Skill: Phaser Design Patterns

If you are building a Phaser game and every new feature keeps adding technical debt and hidden dependencies, this skill helps you structure the project without overengineering.

Start here:

- `./.agents/skills/phaser-design-patterns/README.md`
- `./.agents/skills/phaser-design-patterns/references/patterns-map.md`
- `./.agents/skills/phaser-design-patterns/references/phaser-snippets.md`

## Roadmap

- More game dev skills (architecture, gameplay systems, tooling).
- More reference material with real-world production cases.
- Copy-ready templates for Phaser projects.

---

If you like building games with code that stays maintainable, this repo is for you.

> Note: This project is currently being built and will be continuously updated.
