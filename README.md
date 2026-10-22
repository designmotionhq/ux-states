# ux-states

A Claude Code skill that makes the agent handle every state a screen can actually be in: **loading, empty, error and partial**.

Ask an agent for a list, a table or a form and you usually get the happy path. The spinner never ends, an empty table says "No data", and a failed request disappears into the console. This skill fixes that, and only for the states the component can really be in (no offline banner on a page that can't go offline).

## What it does

- **Loading**: skeletons shaped like the real content, a spinner only for single actions, context and a cancel action when it takes long.
- **Empty**: tells the user why it's empty and what to do next, instead of "No data".
- **Error**: names what failed and the next step (retry, or another way out), instead of a generic "Something went wrong".
- **Partial**: when data comes from several sources or pages, shows what loaded and what didn't.

## Install

```
/plugin marketplace add designmotionhq/ux-states
/plugin install ux-states@ux-states
```

Or copy `skills/state-completeness/` into `.claude/skills/` in your project.

The skill triggers on its own when you build anything that fetches, submits or waits. No command to learn.

## Where it comes from

`ux-states` is one of the nine skills of [UX Engine](https://designmotionhq.com/ux-engine?utm_source=github&utm_medium=readme&utm_campaign=ux-states), a paid Claude Code plugin that also covers intent, hierarchy, forms, feedback, responsive layouts on a phone, a per-project design system and audits. This one is free, under MIT.

Made by designmotionhq.
