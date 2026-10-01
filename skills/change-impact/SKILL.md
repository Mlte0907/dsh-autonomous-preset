---
name: change-impact
description: Use after editing, renaming, deleting, or changing the signature, default, return shape, or behavior of any code symbol — before calling the work done — to find every place that still depends on the old behavior and report what the change actually reaches. Fixes the pattern where repairing one site quietly breaks another.
---

# Change Impact

A change is done when you know what else reads what you changed — not when the edited file compiles.

## Steps

1. **List what you actually changed.** Names, signatures, defaults, return shapes, error cases, side effects — observable facts, not file names.

2. **Find every consumer of each changed name.** Search the whole workspace, not just the package you edited. Include tests, configuration, generated files, and string literals that name the symbol — registration tables, schema maps, dispatch keys, prompt text.

3. **Read each match.** A reference you have not opened is not a result. Decide for each one: still correct, now broken, or never applied.

4. **Run the checks that own the affected areas.** Narrowest first: the test files covering the touched consumers, then the package suite. "It compiles" is not this step.

5. **Report what the change reaches.** Name the consumers you found and what happened to each. If a search came back empty, say so with the pattern you ran.

## An empty search is not proof

A search that returns nothing proves only that this pattern matched nothing. It does not prove the symbol is unused. Before concluding "no callers, safe to delete", confirm the search covered:

- the whole workspace, not one directory;
- alternate spellings — exported name, local alias, the file name in kebab-case, the string form used in registration;
- non-code references — configuration, fixtures, and documentation that pins behavior.

When those are not ruled out, write "no callers found by search; not verified" rather than "unused".

## When to skip

A comment, a doc string, or a typo fix inside one function body changes no interface. Skip the sweep and say you skipped it.
