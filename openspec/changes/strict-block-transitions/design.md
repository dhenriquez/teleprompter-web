## Context

In the current implementation of voice tracking, the candidate search window looks ahead up to 22 words from the current pointer (`curr + 22`). Although a preliminary block boundary check existed, it allowed lookahead into the subsequent block as soon as the pointer was within 5 words of the block's end (`isNearBlockEnd <= 5`). In scripts where identical technical keywords (such as "Pin", "cable", "ESP32", numbers) repeat across adjacent blocks, this allowed the matcher to latch onto repeated words in future paragraphs prematurely, triggering cascading paragraph skips while the speaker was still reading.

## Goals / Non-Goals

**Goals:**
- Completely eliminate lookahead into future blocks while the speaker is reading within the current block.
- Require that block transitions occur strictly at the end of the current paragraph.
- Enforce "front-door" entry into the subsequent block: matches in Block $N+1$ are only permitted within its initial opening phrase (first 4 words).
- Provide terminal tolerance so speakers aren't stranded if speech recognition drops an obscure terminal abbreviation.

**Non-Goals:**
- Modifying speech recognition providers or audio streaming.
- Changing manual scrolling or keyboard shortcut behaviors.

## Decisions

### 1. Hard-Capped Search Window During Intra-Block Reading
- **Decision**: While `curr < blockEnd`, `maxLookahead` is capped at `Math.min(blockEnd + 1, curr + 22)`.
- **Rationale**: By definition, candidate indices `j` cannot reach or evaluate any word belonging to future blocks. This provides a mathematical guarantee that no keyword in a future block can be matched early.
- **Alternatives considered**: Soft penalty score for future blocks. Rejected because identical words with phrase bonuses could still overcome soft penalties.

### 2. Front-Door Entry Only for Next Block
- **Decision**: When the speaker finishes Block $N$ (`curr >= blockEnd`), candidates in Block $N+1$ are evaluated only if `j - nextBlockStart <= 4`.
- **Rationale**: Prevents jumping 20+ words into Block $N+1$ just because a repeated word (like "Pin") appears midway through the next paragraph. The user must start reading the opening of the paragraph.

### 3. Graceful Terminal Tolerance
- **Decision**: Transition unlocks if `curr === blockEnd`, or if `curr === blockEnd - 1` and the spoken audio contains a 2-word phrase matching the opening of Block $N+1$.
- **Rationale**: Prevents edge-case stalls if Google Speech API drops a trailing punctuation mark or symbol (e.g. "-C" in "USB-C").

## Risks / Trade-offs

- **[Risk] Speaker skips the final sentence of a paragraph** → **[Mitigation]** The user can click any word in the next block to immediately reposition the pointer, or continue reading the opening words of the next block.
- **[Risk] Extremely short blocks (< 3 words)** → **[Mitigation]** The opening window (`j - nextBlockStart <= 4`) covers the entire block if the block is very short, ensuring seamless progression.
