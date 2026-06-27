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

## License/self-host wording reconciled to Ernst's canonical voice
- No Ernst PR on vivijure-local-backend yet; his canonical self-hosted voice is established across the
  org (postern #79 "self-hosted privacy note + NOTICE + README license line"). Mirrored it + this
  repo's committed NOTICE so the public framing is one voice:
  - License section now: "[AGPL-3.0-only](LICENSE). ... software you self-host; if you run it as a
    network service for others, you must offer them the complete corresponding source under the same
    license ... See NOTICE for the short version. ... Skyphusion Labs operates nothing here, so we
    hold none of your data ... A labor of love, given freely ... not for sale, and not to be resold
    as a SaaS." (Ernst's postern license-line structure + this repo's NOTICE distinctive lines.)
  - Mermaid caption privacy line aligned to canonical: "Skyphusion Labs operates nothing here, and
    holds none of your data." (was "skyphusion hosts nothing and sees nothing").
- Did NOT link a PRIVACY.md (this repo has none yet; postern has one). If Ernst's #2 adds a
  vivijure-local-backend PRIVACY.md / revises NOTICE, re-sync the README license line then.
- Verified: render clean via gh api /markdown (mermaid + GIF render, license voice present); no dashes.
- PR #3 amended + force-pushed. Pinged lead to review + merge the complete README.

## Final: Ernst approved (A) + added acceptable-use pointer
- Ernst confirmed he'd read the CURRENT README and chose (A): keep the reconciled License wording
  (postern-#79 AGPL network-service sentence + "Skyphusion Labs operates nothing here, so we hold
  none of your data" + NOTICE labor-of-love/not-for-SaaS lines). No further wording changes.
- Per Ernst's request, added a one-line acceptable-use pointer under License:
  "**Acceptable use:** no CSAM and no non-consensual imagery, ever -- see ACCEPTABLE-USE.md."
  ACCEPTABLE-USE.md lands in Ernst's PR #4 (not on this branch), so the link resolves at the paired
  #3+#4 merge (Mackaye merges them together). No dashes; amended + force-pushed PR #3.
- NOTICE stays as-is (studio-matching); README owns the homelabber self-host framing. Future
  License/self-host wording changes: heads-up Ernst to keep NOTICE in sync.
- Session wrap: stood down clean. All work durable + pushed.
