# Coding agent rules

These rules are the global configuration of the user. They apply in every repository.

A repository can have its own instructions. Follow them where they agree with the rules below.
If an instruction of the repository disagrees with a rule below, the rule below governs.

## Git

- Never run `git commit` or `git push` without explicit permission from the user.
- When Codex creates a commit, append `Co-authored-by: codex <codex@openai.com>` as a commit trailer after a blank line. Omit it only when the user explicitly requests sole authorship or no Codex attribution.

## Environment

- Never install packages using `pip install` or `micromamba install`. The environment is managed by the user.
- By default run Python commands inside the `super` conda environment: prefix with `micromamba run -n super` or activate first with `micromamba activate super`. You can override this behavior when the context requires a different environment (for example, `synapse-net`)

## Testing

- Write unit tests for new software features. Do not use inline Python smoke tests. 

## Compatibility

- Do not assume that legacy behavior must remain.
- Prefer a clean interface for new work, unreleased work, and redesigns.
- Remove obsolete compatibility code in the task scope when no compatibility contract requires it.
- Follow an explicit user request or repository policy that requires compatibility.
- Ask for guidance if the correct choice is unclear and the change can break:
  - a published API;
  - stored data;
  - a wire protocol;
  - compatibility for known downstream users.
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
- Never modify `__init__.py` files unless explicitly asked to.
- When adding a `# noqa` comment, never include the error type: write `# noqa`, not `# noqa: E402`.

## Code style
- No section markers in code: no heavy separator line like `# ---------------------------------------------------------------------------`, and no inline label like `# -- encoder --`.
- No excessive space-padding anywhere for visual column alignment: inline comments, docstring argument lists, dict entries, tuple/list elements, printed and logged output, etc. Use single spaces throughout, and always exactly one space after a colon. Write `x = foo(x)  # (B, N, D)`, not `x = foo(x)     # (B, N, D)`; write `"key": value`, not `"key":    value`; write `x: (B, C, H, W)`, not `x:          (B, C, H, W)`; write `print(f"Cosine Similarity: {cos_sim:.6f}")`, not `print(f"Cosine Similarity:   {cos_sim:.6f}")`. You break this rule most often when you write several labels one after another. Read back every block of labels, and delete the padding.
- Never use the Unicode arrow `→`. Use `->` instead.
- Never use the em dash `—`. Use `-` instead.
- Never name module-level variables with a leading underscore.
- Don't embed leading whitespace in string literals for output indentation: write `print(f"Key: {value}")`, not `print(f"  Key: {value}")`.
- Organize the imports of a file into blocks that one blank line separates. The blocks come in this order: the standard library, the scientific utilities, the GUI packages, `torch`, the packages that the user develops, and the current project. Give every package of the user its own block. Order these blocks from the most general package to the most specific one: `elf`, then `torch_em`, then `micro_sam`. A package that contributes more than one import line also gets its own block. Inside a block, put the shortest line first and the longest line last:
  ```python
  import os
  import math
  import argparse

  import numpy as np
  import pandas as pd
  import matplotlib.pyplot as plt

  import napari
  from qtpy import QtWidgets

  import torch
  import torch.nn.functional as F

  from elf.evaluation import dice_score

  from torch_em.data import MinInstanceSampler

  from micro_sam.training import train_sam

  from stable_embeddings.loss import masked_mse_loss
  from stable_embeddings.rotation import apply_geometric_rotation
  ```
- Always structure scripts with functions: put all logic in named functions, call them from `main()`, and guard execution with `if __name__ == "__main__": main()`.
- Never align continuation lines to the opening parenthesis. Always try to fit a call on one line first. Only wrap if it exceeds 120 chars. If it must wrap, use a single extra indent level with the closing paren on its own line:
  ```python
  # good
  x = fn(a, b, c)
  x = a + b + c + d
  x = fn(
      a, b, c, keyword=value
  )
  # bad
  x = fn(a, b, c,
         keyword=value)
  x = (a + b
       + c + d)
  ```
- This applies to `argparse` too. Write `parser.add_argument("--foo", default=x, help="...")` on one line.

## Behavior

- Apply YAGNI to new features. Work down this ladder and stop at the first step that solves the task:
  1. Does this need to exist? If no, skip it.
  2. Already in this codebase? Reuse it, do not rewrite.
  3. Does the standard library do it? Use it.
  4. Is there a native platform feature? Use it.
  5. Does an installed dependency do it? Use it.
  6. Does one line do it? Write one line.
  7. Only then, write the minimum that works.