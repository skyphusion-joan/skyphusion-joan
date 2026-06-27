# Run-log: vivijure-local-backend public README (the flagship front door)

Owner: Joan. Task from lead (public-release prep #3): the public README for
skyphusion-labs/vivijure-local-backend (homelabber flagship, prepping to go public). Novice-first
design DNA (easy floor + learning ladder). Work in /home/joan/dev/vivijure-local-backend, joan gh.

## What I did
- Cloned the repo (was not in joan's tree). Read the grounding sources: existing README, docker-compose.yml,
  .env.example, src/vivijure_local/announce.py (the exact "ready banner"), docs/proof/RESULTS.md +
  report.json, docs/INTEGRATION.md, docs/HOMELABBER.md headers, docs/architecture.md, NOTICE/LICENSE.
- Rewrote README.md novice-first to the locked order: roots -> one-command quickstart (the win:
  docker compose up -> copy-paste banner -> paste into the studio "Local (your GPU)" door) -> inline
  demo that PLAYS -> proven numbers -> AGPL/self-host -> optional "go deeper" ladder below the win.
- Inline demo: GitHub's markdown sanitizer STRIPS <video> (verified via gh api /markdown), so I
  generated a committed lightweight GIF (docs/proof/sample_standard.gif, 1.4MB, ffmpeg from the
  existing proven sample_standard.mp4 -- derived, not new footage) and embedded it as a markdown
  image (always animates inline). Verified via gh api /markdown that it renders as an <img>.
  Full-quality mp4s + keyframe linked from docs/proof/.
- Numbers quoted verbatim from RESULTS.md (draft 38.6s/512x320/97f; standard 125.6s/704x512/121f;
  peak ~10.4GB; ~6GB headroom; cold load 39.7s). Honest trade framing (16GB ceiling vs datacenter),
  not a cloud-product comparison.

## Verification
- All in-repo links verified to resolve. Numbers cross-checked vs RESULTS.md. No en/em dashes.
- Quickstart + banner text mirror docker-compose.yml + announce.py exactly.
- gh api /markdown render: parses clean; GIF embeds as inline <img>.

## Flags / coordinate
- License framing mirrors NOTICE; flagged for reconciliation with Ernst's license pass (#2).
- New 1.4MB committed asset (sample_standard.gif), derived from existing proof mp4.
- Public flip waits for the audit + Conrad (parallel prep).

## Output
PR #3: https://github.com/skyphusion-labs/vivijure-local-backend/pull/3 (branch docs/public-readme,
author skyphusion-joan). Amended once to swap <video> -> inline GIF.
