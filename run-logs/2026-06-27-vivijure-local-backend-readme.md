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

## Additions (from Conrad, via lead): mermaid diagram + credits
- Added a mermaid self-host architecture diagram (house ICD/mermaid standard), placed BELOW the
  quickstart win (new "How it works (you self-host everything)" section before License). Shows: your
  GPU box (docker compose up -> backend LTX i2v + bundled cloudflared quick tunnel) -> public
  trycloudflare URL -> your own studio Worker "Local (your GPU)" door -> token-gated i2v request
  back through the tunnel -> renders on your GPU -> clip to your R2 -> studio. Trust boundary drawn
  via "YOUR ..." subgraph titles + caption: "every box is yours; skyphusion hosts nothing and sees
  nothing."
- Added a Credits block at the bottom (the band): Conrad ([@skyphusion], direction) + Mackaye,
  Strummer, Rollins, Joan, and ERNST (legal) with skyphusion-* gh handles. Conrad flagged "include
  our latest dude" -> Ernst is ON the list.
- Verified via gh api /markdown: mermaid fence renders as <pre lang="mermaid"> (GitHub renders it as
  a diagram), Ernst handle present, all crew handles present. No dashes. Amended PR #3 (force-push).
