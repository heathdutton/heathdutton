<picture>
  <img src="https://ghlogo.heathdutton.workers.dev/nim-ai/betterprompt?ratio=3:2" alt="betterprompt" width="600">
</picture>

# Git Merge for Prompts

## The Problem

Prompt engineering has a versioning problem that code solved decades ago.

Say you ship a base prompt and users start customizing it for their needs. Later, you release an improved version with better instructions. Now users face an impossible choice: lose their customizations, or miss your improvements.

This is the same problem `git merge` solves for code.

## The Solution

[betterprompt](https://github.com/nim-ai/betterprompt) implements semantic three-way merge for natural language. It treats sentences as the unit of change (like git treats lines) but uses embeddings to understand meaning. This is primarily for prompt engineering but could make sense inside shared text editors or documentation systems.

```typescript
import { merge } from '@nim-ai/betterprompt';

const result = await merge(baseV1, baseV2, userCustom, {
  conflictStrategy: "prefer-c"  // preserve user customizations
});
```

Given three versions - original base, upgraded base, user customization - it produces a merged result that incorporates both the upgrade and the user's changes.

## How It Works

1. **Segment** - Break text into semantic units (sentences, paragraphs, or sections)
2. **Embed** - Generate vector embeddings for each unit using Model2Vec
3. **Align** - Match units across versions using cosine similarity
4. **Merge** - Apply three-way merge logic with conflict detection
5. **Reconstruct** - Rebuild the merged text preserving formatting

The merge logic follows familiar rules:

| Original → Upgraded | Original → User | Result |
|---------------------|-----------------|--------|
| unchanged | unchanged | Keep as-is |
| changed | unchanged | Apply upgrade |
| unchanged | changed | Preserve customization |
| changed | changed | Conflict (configurable) |

When both versions modify the same content, you choose the strategy: prefer user, prefer upgrade, concatenate both, or defer for manual resolution.

## No LLM Required

The key constraint was avoiding LLM calls for the core merge. LLMs are slow, expensive, and introduce non-determinism.

Instead, betterprompt bundles Model2Vec embeddings (7.5MB) with a character-frequency fallback. Typical merges complete in under 100ms. The library runs in Node.js and browsers via ONNX Runtime.

LLMs are supported as optional extensions for conflict resolution when you want smarter handling of complex conflicts.

## Try It

**[Live Demo](https://nim-ai.github.io/betterprompt)** - paste your prompts and see the merge in action.

```bash
npm install @nim-ai/betterprompt
```

---

[View on GitHub](https://github.com/nim-ai/betterprompt) ・ [Back to Profile](https://github.com/heathdutton)
