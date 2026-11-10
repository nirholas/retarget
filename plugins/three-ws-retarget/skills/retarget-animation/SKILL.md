---
name: retarget-animation
description: Play animation clips on any humanoid GLB in Three.js with @three-ws/retarget: canonicalize bone names (Mixamo, VRM, Daz, Unreal and more), retarget clips, and drive them with a crossfading manager. Use when an avatar freezes in a T-pose or clips from one rig must play on another.
---

# Retarget animations onto any humanoid GLB

`@three-ws/retarget` maps any humanoid rig's bone names onto a canonical 52-bone skeleton, retargets clips with rest-pose and hip-axis correction, and drives them with a crossfading runtime. Understands Mixamo, Avaturn/Wolf3D, VRM 0.x and 1.0, VRoid, Daz/Genesis, MakeHuman, Unreal mannequin, HumanIK/Maya and Blender `.L`/`.R` names. Non-humanoid rigs are refused, not half-animated.

```bash
npm install @three-ws/retarget three   # three >= 0.150 is a peer dependency
```

## Retarget one clip

```js
import { AnimationMixer } from 'three';
import { GLTFLoader } from 'three/addons/loaders/GLTFLoader.js';
import { MeshoptDecoder } from 'three/addons/libs/meshopt_decoder.module.js';
import { canonicalizeGLBBones, retargetClipToObject, parseClipJSON } from '@three-ws/retarget';

const raw = await fetch('https://three.ws/avatars/xbot.glb').then((r) => r.arrayBuffer());
const { buffer } = canonicalizeGLBBones(raw);          // byte-safe rename in memory
const loader = new GLTFLoader();
loader.setMeshoptDecoder(MeshoptDecoder);               // three.ws GLBs are meshopt-compressed
const gltf = await loader.parseAsync(buffer, '');

const clipJSON = await fetch('https://three.ws/animations/clips/walk.json').then((r) => r.json());
const result = retargetClipToObject(parseClipJSON(clipJSON, 'walk'), gltf.scene);
// { clip, coverage, matched, total, dropped, hipScale, face }
if (result.clip) new AnimationMixer(gltf.scene).clipAction(result.clip).play();
```

When bone coverage is below `MIN_COVERAGE` (0.5) `result.clip` is `null` with the `coverage` and `dropped` breakdown. That means the rig is not a humanoid: report it, do not force-play.

## Let the runtime drive

```js
import { AnimationManager } from '@three-ws/retarget';

const manager = new AnimationManager();
manager.attach(gltf.scene);
if (manager.supportsCanonicalClips()) {
  manager.setAnimationDefs([
    { name: 'idle', url: 'https://three.ws/animations/clips/idle.json', loop: true },
    { name: 'wave', url: 'https://three.ws/animations/clips/wave.json', loop: false },
  ]);
  await manager.loadAll();
  manager.crossfadeTo('idle', 0.25);
}
// every frame: manager.update(deltaSeconds);
```

`AnimationManager` crossfades base loops, layers one-shot overlays and rejects clips that would leave the character fallen.

## Other exports

`canonicalizeBoneName(name)`, `canonicalizeJointNodes(json)`, `canonicalizeArmatureOrientation(json)`, `CANONICAL_BONES`, `retargetClipToRig`, `retargetClip`, `scaleClipSpeed(clip, factor)`, `measureHipsTiltDeg`, `CANONICAL_REST`, `CANONICAL_REST_WORLD`, `MIN_COVERAGE`.

## Debugging a rig that will not animate

1. Decode meshopt before loading (`setMeshoptDecoder`), or bones may never resolve.
2. Run `canonicalizeGLBBones` and inspect `coverage` and `dropped` from the result.
3. For an unknown bone convention, add the mapping to the canonicalizer instead of building a rig allowlist.
