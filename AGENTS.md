# Coding agent rules

These rules are the global configuration of the user. They apply in every repository.

A repository can have its own instructions. Follow them where they agree with the rules below.
If an instruction of the repository disagrees with a rule below, the rule below governs.

## Git

- Never run `git commit` or `git push` without explicit permission from the user.

## Environment

- By default run Python commands inside the `super` conda environment: prefix with `micromamba run -n super`. 
You can override this behavior when the context requires a different environment (for example, `synapse-net`)

## Testing

- Write unit tests for new software features. Do not use inline Python smoke tests. 

## Compatibility

- Do not assume that legacy behavior must remain. Prefer a clean interface, and remove obsolete compatibility code in the task scope.
- Keep compatibility when a contract requires it: a published API, stored data, a wire protocol, known downstream users, or an explicit request. Ask for guidance if unclear.
- Do not remove unrelated compatibility code.

## Comments and Documentation

- Do not add a comment or docstring only because you added or changed code.
- Prefer clear names, types, interfaces, and code structure.
- Add prose only when it explains a contract, intent, rationale, invariant, constraint, unit, external issue, or non-obvious risk.
- Do not narrate control flow, restate a signature, repeat code, or add generic introductions and conclusions.
- Keep required public API documentation, license text, generated-file markers, directives, and useful examples.
- Keep necessary prose concise. Prefer one short line for the non-obvious "why". A comment that runs to several lines is almost always too long; cut it down.
- Write the prose you keep in Simplified Technical English. See the `simple-technical-english` skill for the full profile.

## Code quality

- Always run `flake8 --max-line-length=120` on any code you add before considering the task done.

## Code style

- No section markers in code: no separator line like `# ----------------`, and no inline label like `# -- encoder --`.
- No space-padding for visual column alignment, anywhere: inline comments, dict entries, printed and logged output. Use single spaces, and exactly one space after a colon. Write `x = foo(x)  # (B, N, D)`, not `x = foo(x)     # (B, N, D)`. You break this rule most often when you write several labels in a row; read back every block of labels and delete the padding.
- Never use the Unicode arrow `→`. Use `->` instead.
- Never use the em dash `—`. Use `-` instead.
- Never name module-level variables with a leading underscore.
- Don't embed leading whitespace in string literals for output indentation: write `print(f"Key: {value}")`, not `print(f"  Key: {value}")`.
- Use `pathlib.Path` for filesystem paths, not `os.path`.
- Organize imports into blocks that one blank line separates, in this order: standard library, scientific utilities, GUI packages, `torch`, the user's packages from general to specific (`elf`, then `torch_em`, then `synapse_net`), the current project. Each of the user's packages gets its own block, as does any package with more than one import line. Inside a block, shortest line first:

  ```python
  import csv
  from pathlib import Path

  import h5py
  import numpy as np
  import pandas as pd
  import configargparse
  import matplotlib.pyplot as plt

  import torch
  import torch_em
  from synapse_net.inference.actin import segment_actin
  ```
- Structure scripts with functions: put the logic in named functions, call them from `main()`, and guard with `if __name__ == "__main__": main()`.
- Never align continuation lines to the opening parenthesis. Fit the call on one line when it stays within 120 chars; otherwise wrap with one extra indent level and the closing paren on its own line:
  ```python
  # good
  x = fn(a, b, c)
  x = fn(
      a, b, c, keyword=value
  )
  # bad
  x = fn(a, b, c,
         keyword=value)
  ```

## Behavior

- When a request is ambiguous, ask the user for context before you reach for tools. One question is cheaper than a search or an edit built on a wrong guess.
- Write concise responses. Do not give long-winded explanations; state the result and the one thing the user must know.
- Apply YAGNI to new features. Work down this ladder and stop at the first step that solves the task:
  1. Does this need to exist? If no, skip it.
  2. Already in this codebase? Reuse it, do not rewrite.
  3. Does the standard library do it? Use it.
  4. Is there a native platform feature? Use it.
  5. Does an installed dependency do it? Use it.
  6. Does one line do it? Write one line.
  7. Only then, write the minimum that works.