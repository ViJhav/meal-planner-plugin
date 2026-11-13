# Meal Planner for Claude Code

A Claude Code plugin that connects Claude to your household in the Meal Planner
web app: browse the meal library, read the calendar and groceries, confirm what
you ate, and place meals or open a household vote.

This repository holds only the built plugin. Nothing here needs `npm install`.

## Before you install

- Node.js 20 or newer (`node --version`).
- A Meal Planner account with a household, set up in the web app first. The
  plugin signs in to that account; it cannot create one.

## Install

In Claude Code, add this repository as a plugin marketplace and install the plugin:

```
/plugin marketplace add ViJhav/meal-planner-plugin
/plugin install meal-planner@meal-planner
```

Then run `/meal-planner:setup`. It checks Node and walks you through sign-in.

## Sign-in

Sign-in happens in your own terminal, never in a Claude chat: the link you paste
back carries your session. Run the command the setup skill gives you, open the
emailed link, and paste the final app URL into the terminal prompt. The session
is stored in `~/.config/personalos/meal-planner-mcp/auth.json`, readable only by
you. Run the same command with `sign-out` in place of `sign-in EMAIL` to clear it.

Your household's data stays in the Meal Planner service, scoped to your household.

## Tools

- `search_meals` and `get_meal` browse the household library.
- `read_calendar` lists entries and cook batches for an inclusive date range.
- `read_day_totals` returns planned and recorded protein and fiber separately.
  The protein anchor is a planned assumption and is returned alongside the targets.
- `read_groceries` returns the app's grocery list and Reminders-ready text.

For intake, use `read_calendar` to review the signed-in person's day. Confirm the
whole day once in chat, including which meals were eaten, skipped or replaced,
and any extras. Call `mark_entry_eaten` for each confirmed planned meal or
unchanged takeout using its entry ID and date. For a substitution or extra, call
`record_ad_hoc_intake` with the actual food and leave the original entry
unrecorded. These foods appear as snacks on the calendar. Supply the basis for
any grams and use `null` when unknown. If a write has an uncertain outcome,
read the calendar before attempting it again to avoid duplicate intake.

Marking an entry eaten checks its current date and planner slot before calling
the existing RPC. A calendar move concurrent with that call can change the slot
between the check and the write.

Use `remove_entry` with the entry ID from `read_calendar` after the user asks to
remove that entry. Repeating the same removal leaves the entry absent.

Use `read_calendar` to preview the destination before calling `place_entry`
or `move_entry`. Show the user any entry the write would replace and wait for
confirmation. Pass that entry's ID and exact `updatedAt` value as
`expectedOccupant`, or pass `null` for an empty slot or a stackable snack slot.
Eaten entries remain as history. If the destination has changed, read it again
and confirm the new preview. If the result is uncertain, read the calendar
before trying again. Placement can use an existing batch or create a new cook
from a library meal.

Use `open_decision` after the user agrees to the vote scope and candidates and
asks to open it. Pass distinct active meal IDs from `search_meals`. Candidates
use the household's saved default filling. A dated vote uses the web app's
presets. A `cooks_week` vote can specify how many meals should win. An opened
vote is returned with its ID and a separate notification status. If creation is
uncertain, check open votes in the web app before attempting another create.
Household members rank in the web app.
