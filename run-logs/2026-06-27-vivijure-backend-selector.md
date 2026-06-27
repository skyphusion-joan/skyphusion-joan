# Run-log: vivijure planner backend selector ("one studio, two honest doors")

Owner: Joan (frontend/extraction). Session: isolated background build.
Repo: /home/joan/dev/vivijure (studio control-plane worker + planner UI).
Task from lead (mackaye): add a backend SELECTOR to the planner UI -- "Local (your GPU)"
vs "RunPod (datacenter)" -- built against the EXISTING module-host hook contract, backend
agnostic, honest two-door framing, capability/limits surfaced. PR under joan's own identity.

## Contract study (docs/CONTRACT.md, vivijure-module/2)

- The backend choice IS the `motion.backend` hook (pick_one): "keyframe -> shot clip (GPU or
  cloud)". Each backend (local consumer GPU, RunPod datacenter, any cloud i2v) is a separate
  `motion.backend` MODULE. The selection is carried on the EXISTING wire field `motion_backend`
  (render submit 2.18 / 2.20 / 2.23; renderOverrides 2.18.1; poll view unaffected).
- Projection rules (sec 5): `pick_one` -> a chooser; module order comes pre-sorted from
  `data.hooks[hook]` (ui.order then name); the frontend consumes verbatim, never re-sorts.
- Manifest (sec 4): name, version, api, hooks, provides[{id,label}], config_schema{ConfigField},
  ui{section?,icon?,order?}. `binding` is stripped from the public projection.
- So the seam already exists. The render-config panel already renders a bare motion.backend
  <select> (planner-render-config.js renderMotionPicker) that sets motion_backend. My job is to
  turn that bare dropdown into an HONEST two-door selector with capability/limits, WITHOUT
  inventing wire fields.

## Existing-code findings

- public/planner-render-config.js: `renderMotionPicker(mods, selected)` builds
  `<select id="planner-motion-backend">` ONLY when mods.length > 1, options = moduleLabel
  (provides[0].label || name). collect() -> out.motion_backend; restore() reads it back. This is
  the surface I will upgrade. collect()/restore() contract on #planner-motion-backend must be
  preserved (planner.js:204-205, 681, 956-957 depend on collectForSubmit/collect/restore).
- public/planner-registry.js: `ownGpuModule()` and `cloudMotionModules()` split local-vs-cloud
  by HARDCODING the module name "own-gpu". planner.js finalize/animate flow (lines ~5800-5950)
  reuses that split (ownGpuModule/cloudMotionModules/gpuMotionLabel/cloudModelOptions).
- IMPORTANT nuance: the EXISTING "own-gpu" module (modules/own-gpu) is NOT a true local
  consumer GPU -- its header says it runs i2v on a vivijure-backend RunPod serverless endpoint
  (BYO-endpoint). The genuinely-LOCAL consumer-GPU backend is the NEW module Rollins is building.
  It will be a new motion.backend module with its OWN name. So any local-vs-cloud logic keyed on
  the literal "own-gpu" will MISCLASSIFY the new local backend as cloud. (Gap, see below.)
- planner.js is detected by grep as a "binary" file (stray byte); use `/usr/bin/grep -a`. The
  shell's bare `grep` is a broken profile shim -- use /usr/bin/grep throughout.

## GAP flagged to lead/Rollins (manifest fields needed; do NOT invent on the wire)

The manifest carries NO field for the honest "door" narrative or an explicit backend CLASS, and
the local-vs-cloud distinction is currently inferred from a hardcoded module name. To make the
two-door framing a true PROJECTION (not hardcoded copy), the module MANIFEST should carry, as
OPTIONAL additive fields (no MODULE_API bump; they fit "self-assembling UI hints" under `ui`):

  - ui.locality : "local" | "cloud"   -- replaces the hardcoded own-gpu name check; lets the UI
                                          tag a door "Local (your GPU)" vs "Datacenter" generically
                                          and sort local-first.
  - ui.cost     : short chip string, e.g. "no per-render cost" / "pay per render (per second)".
  - ui.blurb    : one honest sentence per door (the positioning copy).
  - ui.limits   : string[] honest capability bullets, e.g.
                    local: ["16GB VRAM floor (4060 Ti class)","lower resolution/frame ceiling",
                            "scales with your card"]
                    runpod: ["datacenter GPUs","top quality ceiling","billed per second"]

Frontend behavior until these land: the selector renders from what the registry DOES carry
(provides[].label as title + extra capability bullets, config_schema numeric min/max as a
knob-range "limits" line) and consumes ui.locality/cost/blurb/limits IF present, rendering
nothing fabricated when absent. The moment Rollins adds the fields to the local + runpod module
manifests, the honest copy appears with zero further frontend change. Recommended values above
are for Rollins/Mackaye to place in the module manifests (my lane: report, not reach into module
dirs).

## Plan

1. Replace renderMotionPicker with an honest door-card selector in planner-render-config.js:
   - one card per installed motion.backend module (registry order, verbatim).
   - card = title (provides[0].label) + optional locality tag/cost chip/blurb/limits (projected
     IF present) + extra provides labels as capability bullets + schema-derived knob ranges.
   - keep authoritative #planner-motion-backend value (hidden select for >=2 modules) so
     collect()/restore() are byte-compatible. Single backend = informational card, no forced value.
2. CSS for the selector in styles.css (vanilla, house tokens).
3. node --check the JS. Open PR under joan identity.

## Progress
- [x] Contract + code study, gap analysis (above).
- [ ] Implement selector + CSS.
- [ ] Verify (node --check).
- [ ] PR under joan identity.

## Implementation (done)

Branch: feat/planner-backend-selector (off origin/main). Files (frontend assets only):
- public/planner-render-config.js: replaced the bare motion.backend <select>
  (renderMotionPicker) with an honest door-card selector (renderBackendSelector + backendDoor
  + selectBackend/syncBackendDoors). Each installed motion.backend module = one door, in
  registry order (verbatim). collect()/restore() unchanged in contract: the authoritative value
  still lives on #planner-motion-backend (a hidden <select> when there are >= 2 doors; the radios
  drive it). One backend = a single informational door, NO #planner-motion-backend element, so
  collect() emits no motion_backend and the core resolves the only door (prior behavior kept).
  Each door projects: title (provides[0].label); OPTIONAL ui.locality tag / ui.cost chip /
  ui.blurb / ui.limits (rendered only if the manifest declares them); extra provides[].label as
  capability bullets; and a registry-truth "Tunable:" line derived from config_schema numeric
  min/max when ui.limits is absent. Nothing per-backend is hardcoded.
- public/styles.css: .planner-backend-* styles (house tokens; doors stack responsively;
  selected door gets an accent border + tint; local/cloud/cost tags color-coded).

## Verification

- node --check public/planner-render-config.js: OK (the sanctioned gate; these assets are not
  under tsc).
- House style: no en/em dashes in either committed file (verified).
- Behavioral: headless Chrome is blocked in this isolated build session (exits 144, no output,
  even under xvfb-run; the claude.ai Chrome extension is also not connected here), so live
  browser verification was not possible. Instead wrote a faithful node DOM-shim harness
  (/tmp/domshim_test.js + /tmp/harness/payload.json -- NOT committed) that loads the REAL asset
  and asserts, against a representative two-door GET /api/modules payload:
    26/26 PASS, incl.: two doors rendered in registry order; default = first door; local door
    projects locality/cost/blurb/limits + extra capability bullet; RunPod door (no ui hints)
    shows NO fabricated copy and falls back to schema-derived "Tunable: fps 8-30, motion 1-12";
    collect() -> motion_backend on the existing wire field; picking a door updates collect() +
    highlight; restore() round-trips and re-syncs; single-backend = one informational door, no
    radio, no motion_backend emitted.
  A throwaway static harness (/tmp/harness/index.html) is also left for a human to open in a real
  browser for the visual check.

## GAP -- ACTION for lead (mackaye) / Rollins (manifest is the source of truth, my lane = report)

The honest two-door FRAMING (locality, cost model, capability ceiling) and the local-vs-cloud
CLASS are not yet expressible in the registry. Today the local-vs-cloud split is inferred from a
hardcoded module name ("own-gpu") in planner-registry.js (ownGpuModule/cloudMotionModules) and
the planner.js finalize/animate flow -- which will MISCLASSIFY Rollins's NEW genuinely-local
consumer-GPU module (different name) as cloud. Recommend adding OPTIONAL, additive manifest
fields under `ui` (no MODULE_API bump; documented in CONTRACT.md sec 4 per the maintainer rule):
  ui.locality: "local" | "cloud"   ui.cost: short chip string
  ui.blurb: one honest sentence     ui.limits: string[] honest capability bullets
The frontend selector ALREADY consumes all four (renders nothing fabricated when absent), so the
honest copy appears with zero further frontend change once Rollins sets them on the local +
RunPod module manifests. Recommended values (for the module manifests, not for me to edit):
  local : locality "local",  cost "no per-render cost",
          blurb "Your own card. No cloud, no per-render cost; quality ceiling scales with the
                 card (16GB / 4060 Ti class floor).",
          limits ["16GB VRAM floor (4060 Ti class)","lower resolution / frame ceiling",
                  "scales with your card"]
  runpod: locality "cloud",  cost "pay per render (per second)",
          blurb "Rent datacenter GPUs by the second. Top quality ceiling; you pay per render.",
          limits ["datacenter GPUs","top quality ceiling","billed per second"]
FOLLOW-UP (separate change, flagged not done): once ui.locality lands, retire the hardcoded
"own-gpu" name check in planner-registry.js + the planner.js finalize/animate flow so the local
door is classified by manifest, not by name.

## Progress
- [x] Contract + code study, gap analysis.
- [x] Implement selector + CSS.
- [x] Verify (node --check + node DOM-shim harness 26/26).
- [ ] PR under joan identity (next).

## Follow-up: retire hardcoded "own-gpu" name split -> classify by ui.locality (PR #381)

#379 MERGED. Lead approved the ui.* fields (ui.locality/cost/blurb/limits, additive, no
MODULE_API bump); Rollins setting them on the motion.backend manifests.

Follow-up shipped: PR #381 (branch feat/planner-backend-locality -> main, author skyphusion-joan).
- public/planner-registry.js: new motionLocality(mod) classifies a motion.backend module as
  "local" vs "cloud" by manifest ui.locality ("local"|"cloud"|"datacenter"). ownGpuModule() and
  cloudMotionModules() now use it instead of the literal m.name === "own-gpu" check.
- SAFE ordering (per lead): motionLocality FALLS BACK to the legacy "own-gpu" name check when a
  module does not declare ui.locality, so classification is byte-identical during the rollout
  window. Comment marks the fallback removable once every motion.backend manifest carries
  ui.locality (final cleanup, later follow-up).
- planner.js NOT edited: its finalize/animate flow consumes the registry helpers (ownGpuModule /
  cloudMotionModules via cloudModelOptions / gpuMotionLabel) and has NO own-gpu literal (verified
  by grep), so it inherits the fix.

Verification: node --check OK; no dashes; node harness 8/8 (rollout fallback byte-identical;
ui.locality drives the split when present; manifest beats name; "datacenter" -> cloud). Headless
Chrome still blocked in this isolated session; human does the visual pass.

## Progress
- [x] #379 backend selector (merged).
- [x] #381 ui.locality classification with name fallback (open, ready for review).
- [ ] LATER (flagged, not mine to schedule): remove the name-check fallback once all
      motion.backend manifests carry ui.locality.
