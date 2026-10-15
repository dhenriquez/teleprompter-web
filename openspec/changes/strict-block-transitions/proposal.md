## Why

During long script readings with repeating technical terms (e.g. repeated occurrences of "Pin" across multiple paragraphs), the voice recognition algorithm could prematurely look ahead into subsequent blocks before the user finished reading the current paragraph. This resulted in unexpected cascading jumps across entire blocks, ruining recordings. A strict block-boundary isolation and front-door transition mechanism is required to guarantee the prompter never leaves a block until the speaker completes it.

## What Changes

- **Strict Intra-Block Confinement**: While the speaker has not completed the current block, the lookahead search window is strictly capped at the block's final word. Words belonging to future blocks are completely invisible to the matcher.
- **Front-Door Transition Only**: Moving from Block $N$ to Block $N+1$ requires the speaker to have read the final words of Block $N$, and any match in Block $N+1$ must be strictly located within its initial opening phrase (first 3-4 words), preventing mid-paragraph entry caused by repeated words.
- **Graceful Terminal Tolerance**: Ensures the transition unlocks if the final word or the penultimate phrase is read, so minor recognition drops on final punctuation or symbols do not trap the speaker.

## Capabilities

### New Capabilities
- `strict-block-boundaries`: Enforces strict block isolation during speech recognition and requires front-door entry to advance to subsequent blocks.

### Modified Capabilities
<!-- None -->

## Impact

- Modifies the word matching algorithm in `index.html`.
- No new external dependencies or breaking API changes.
