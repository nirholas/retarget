# AGENTS.md

Instructions for AI coding agents working in or with `@three-ws/retarget`.

## What this is

Retarget animations onto any humanoid GLB. Canonicalizes bone names from Mixamo, Avaturn, VRM/VRoid, Daz/Genesis, MakeHuman, Unreal, and Blender rigs onto one canonical skeleton, retargets clips with rest-pose and hip-axis correction, and drives them with a crossfading runtime, so no rig ever freezes in a T-pose.

## Use it

```bash
npm install @three-ws/retarget
```

Full API reference: [README.md](README.md). Machine-readable summary: [llms.txt](llms.txt). Full text: [llms-full.txt](llms-full.txt).

## Work on it

- Node 20 or newer, ESM only.
- Run the tests with `npm test` before proposing a change.
- Keep runtime dependencies at their current count. Prefer a well-maintained library over new hand-rolled code, and say why in the pull request.
- No mocks, placeholders or TODO comments in committed code.
- Do not use em-dashes or en-dashes in code, comments or docs.

## Source of truth

This repository is exported from https://github.com/nirholas/three.ws/tree/main/packages/retarget. Bug reports belong here; larger changes should be proposed against the monorepo path.
