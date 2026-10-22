---
name: state-completeness
description: "Use for any UI that fetches, submits or waits: lists, tables, search, forms, uploads, dashboards. Covers the loading, empty, error and partial states it can actually be in."
license: MIT
---

# State Completeness

## The rule

Any UI component that fetches data, accepts user input, or handles async work handles every state it can actually be in. Which states those are depends on the component, not on this table:

- **Loading, empty, error, success** apply to anything that fetches or submits. A list fetches; a form submits; both have all four.
- **Partial** applies when data comes from more than one source, or is paginated or streamed. A single fetch has no partial state.
- **Offline** applies when the product is meant to be used without a connection (mobile, field tools, editors with local drafts). Elsewhere it is one line in the gap report, not a requirement.

If the component skips a state that applies to it, add it or surface the omission explicitly. Do not add offline banners and partial-load placeholders to a component that cannot be in those states; that is weight, not completeness.

## Loading state rules

### Skeleton vs spinner
- **Skeleton** when the layout shape is known (cards, lists, tables), preferred
- **Spinner** when it's truly a single action (button loading, modal opening)
- **Progress bar** only when actual progress is measurable (file upload, multi-step)

### Skeleton shape must match real content
A skeleton that doesn't match the final layout shape causes visual jump. Match:
- Number of items (3 skeleton rows for a 3-row list)
- Approximate dimensions
- Same gap/padding

### Loading text
If loading takes >2 seconds, surface context: "Loading 1,247 records…" not just "Loading…".
If loading takes >10 seconds, offer a cancel action.

## Empty state rules

"No data" is not an empty state. It's a UI failure.

Every empty state must answer 3 questions:
1. **Why is this empty?** (you haven't created any yet / nothing matches your filter / you don't have access)
2. **What should the user do?** (clear primary action OR clear "this is expected" message)
3. **How does it help them learn the product?** (illustration optional, but copy must teach)

### Types of empty

| Cause | Treatment |
|---|---|
| First-time use (no data yet) | Onboarding empty state with primary CTA: "Create your first invoice" |
| Filtered/searched | Clear filter affordance: "No invoices match 'overdue'. Clear filter" |
| Permissions / access | Explanation + contact: "You don't have access. Ask your admin to share this." |
| Genuinely empty by design | Confirmation: "All caught up. No pending reviews." |

### Empty state copy rules
- Use "you" not "the user"
- Lead with the cause, then the action
- One CTA max (the most likely next action)
- Skip illustrations if your design system doesn't have them ready; text-only is fine

## Partial state rules

Partial states are the most-skipped state. Handle them:

- Some items loaded, more loading: show loaded items + skeleton placeholders for remaining
- Some fields fetched, some failed: show what's there, mark missing with inline "Failed to load. Retry" not the whole component erroring
- Pagination loading next page: keep current page visible, show loading at bottom

NEVER blank the screen on partial; it's a regression from already-loaded state.

## Error state rules

Generic errors are unacceptable. "Something went wrong" is a bug.

### Error message anatomy

```
[What happened in user terms]
[Why it might have happened] (optional)
[What to do now]
```

Example:
- ❌ "Error: 500"
- ❌ "Something went wrong"
- ✅ "We couldn't save your changes. Your internet might be down. Try again, or copy your draft to keep it safe."

### The fallback message

Code that reads `err.message ?? "<default>"` ships the default to most users. The default follows the same anatomy, specific to this component: "We couldn't load the invoices. Try again." It names the thing and the next step.

### Error placement
- **Inline** (next to the offending field/action): for validation, field errors
- **Toast** (transient banner): for completed-but-failed actions
- **Page-level** (full replacement of content): for catastrophic failures
- **Modal**: only for errors that require an immediate decision

NEVER use toast for errors that require user action; they disappear. Use inline or modal.

### Recovery actions
Every error must offer:
- Retry (if transient)
- Alternative path (if persistent)
- Support contact (if neither works)

NEVER show an error without a path forward.

## Success state rules

Successful actions need visible confirmation. Silence is ambiguous.

### Confirmation types

| Action | Confirmation |
|---|---|
| Inline edit | Field flashes green + checkmark for 1.5s |
| Form submit (stays on page) | Toast "Saved" + visible change in data |
| Form submit (navigates) | Destination page shows context: "Created John Smith." |
| Destructive action | Toast with undo affordance: "Deleted. Undo (5s)" |
| Batch action | Summary: "Suspended 12 users." |

No silent success, no context-free "Success!", no auto-redirect without confirmation, no permanent success banner.

## Offline state rules

Only when the product is meant to be used without a connection (see The rule). Then:

1. **Detect offline** (`navigator.onLine` + failed fetches)
2. **Surface the state** with a persistent banner: "Offline, your changes are saved locally"
3. **Queue mutations** if possible, retry when online
4. **Disable actions** that strictly require network
5. **Don't show errors** for offline-expected failures

Everywhere else, a failed request is an error state with a retry, and offline is one "n/a" line in the checklist.

## Component checklist (use this)

Before declaring a data component done, verify each:

```
[ ] Loading state: matches final layout shape
[ ] Empty state: explains why + offers next action
[ ] Error state: specific message + recovery path
[ ] Success state: visible confirmation, not silence
[ ] Partial state (multi-source, paginated, streamed): degrades gracefully when some data fails
[ ] Offline state (offline-capable products): degraded mode + queue
```

Tick the states that do not apply as "n/a: [why]" (a single fetch has no partial; a desktop admin tool has no offline). If a state that applies is unchecked, the component is not complete. Surface the gap to the user explicitly:

> ⚠️ State coverage incomplete:
> - Empty state not implemented (currently shows nothing)
> - Error state is a generic message with no retry
>
> Add these before shipping.

## Worked example

`examples.md` in this folder: the same list component happy-path only, then with every state it can be in. Read it when the shape of the code is unclear.

## Proportionality

Match the visible weight of this skill to what the request creates.

- **Modifying existing UI** (a colour, a field, a label, one component's
  behaviour): apply the rules silently. No questions, no brief, no block.
  The change inherits what the project already established, and you answer
  normally.
- **Creating a new surface** (a screen, a page, a component that did not
  exist): apply the rules visibly, with the artefact this skill produces.

When in doubt, treat it as a modification. A rule applied quietly is still
applied.

## Override rule

If the user justifies a design pattern this skill refuses, or insists after being warned, state the risk in one line, then build it. These design rules guide; on their own product, the user decides.
