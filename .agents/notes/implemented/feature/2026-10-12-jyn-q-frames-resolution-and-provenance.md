# Agent Note: jyn-q frames, resolution and provenance

Status: implemented

## Problem

The jyn-q submission (PR #13) was held by the 2026-10-12 review on four points.

1. **No user-visible evidence.** The pull request body and comments carried no
   screenshot or recording: neither the pet in the settings selector nor the
   states animating. The per-track `previews/*.gif` were committed but never
   referenced, so the gate was not visibly answered.
2. **No on-disk provenance.** `assets/jyn-q/` had no README, `THIRD_PARTY_NOTICES.md`
   had no jyn-q entry, the README built-in pet table had no row, and `pet.json`
   declared `author: 11726` while the README credits jyn to stushansusu — two
   signatures, no recorded relationship. The review also asked where the static
   illustration behind every frame came from.
3. **The eight states were one drawing.** Measured on the alpha bbox, all eight
   states sat 3.5%-13.9% away from the idle first frame, and 25 groups of the 148
   frames were byte-identical (the loops folded back on themselves; every one-shot
   opened on the same neutral pose). The manifest description claimed head-tilt
   thinking, keyboard-tap bobbing and nodding that the frames did not show, and
   claimed "four phase tracks match the seven phases" where the manifest maps all
   seven.
4. **223x297 was the smallest cell of any built-in pet**, so the default 160 px
   display height would upscale and look soft on a 2x screen.

## Decision

- **Shipped the bouncing redesign that had been written but never regenerated.**
  The author's generator had already been rewritten from a breathing idle to a
  physically-shaped hop (parabolic height, and vertical speed at take-off/landing
  driving stretch and squash), but the committed frames were still the older
  output — which is why `happy/f_000` was byte-identical to `idle/f_000` while the
  description already talked about bouncing.
- **A nominal 320 px of content height, one 340x430 cell, 144 frames** (the `tool`
  loop went 16 -> 12 frames). The first version of this bullet claimed a 2x screen
  would then blit 1:1; the 2026-10-16 review caught it and it is corrected here.
  Display sizing divides the requested size by the **cell** height
  (`spriteScale = display.size / cell.height`), not by the illustration, and the
  illustration only spans the middle of the cell: measured frame by frame it is
  254-383 px tall, median 331. At the default `size=160` the pet therefore draws
  about 123 CSS px tall — on a 2x screen a ~0.74x downsample of the source, better
  than the rejected 223x297 cell's upscale but **not** a 1:1 blit; 1:1 for the full
  cell needs `size = 215`. The shipped package is 5.57 MB against jyn's 113.9 MB,
  doro's 55.5 MB and jyn-foxtail's 14.9 MB, so the resolution is not what makes a
  built-in pet heavy.
- **jyn-q stays out of the npm `files` whitelist.** This branch first added
  `assets/jyn-q` to it; the 2026-10-16 review had the line removed. A fan character
  declared personal non-commercial and non-official must not ride along in the
  published `@linxin666/dsh-pet` tarball, and this repository whitelists only pets
  that are meant to be built-in defaults (whale / whale-refined / ouo-neko /
  blue-throated-bee-eater / decorations). The registry still resolves jyn-q from
  `assets/` in a checkout — the expected-entry list and the `byId` assertion in
  `src/registry.test.ts` stay, because the registry scans the directory and dropping
  them would fail the test — and a published install gets the pet through a Workshop
  install under `$DSH_HOME/pets`, like doro, jyn, jyn-foxtail, long-niang and
  starry-doll.
- **Every loop now carries an odd-harmonic term with a phase offset**, so the hop
  and the sway no longer share a period and t and t+0.5 cannot fold onto the same
  pose; the one-shots open mid-anticipation instead of on the neutral stance. The
  package now ships **144 frames and no two of them are byte-identical** — asserted
  by a new case in `tests/jyn-q-pet.spec.ts`.
- **The description now says what the frames do.** The artwork is one rigid
  illustration, so the text says the whole thing tilts around the foot (±9° for
  the think loop, ±6° for the review loop) and reads as a head tilt, rather than
  claiming separate per-state drawings. The phase line now states that all
  seven activity phases are mapped explicitly.
- **Provenance is on disk**: `assets/jyn-q/README.md`, a `THIRD_PARTY_NOTICES.md`
  section in the Doro format, and a README built-in pet table row (English +
  Chinese + re-recorded `README.i18n.yaml` pair).
- **The illustration's origin is recorded as evidence, not as a claim.** The
  1536x1536 transparent source PNG is AI-generated with Doubao and carries the
  TC260 AIGC label in its XMP metadata naming `doubao` as the content producer;
  the shipped 486x640 master is that file alpha-cropped and LANCZOS-scaled, and
  the frames are foot-anchored renders of the master.
- **The author mapping is written down**: `11726` is the signature the pet.json
  files on this character line carry (jyn, jyn-foxtail, doro), while the README
  and notices credit the GitHub account stushansusu; Doro's notice had already
  recorded that pairing, and jyn-q now records it too.

## Alternatives considered

- **Keeping 223x297 and declaring the low resolution accepted.** Rejected. The
  review offered it, but the larger cell costs little against the pet sizes already
  shipped and turns an upscale into the mild 0.74x downsample recorded above — which
  the README row now states, instead of leaving the reader to infer 1:1.
- **Adding real per-state art (separate poses per state).** Rejected for this
  submission. The pet's premise is one illustration driving a procedural pet; a
  multi-pose set is a different submission, not a fix. The review's own wording
  offered "change the description" or "add real per-state art" as alternatives, so
  the description was corrected and the motion enlarged.
- **Correcting only the description and leaving the ~1-4 deg amplitudes.** Rejected.
  At those amplitudes the states are not distinguishable, which is exactly what the
  review measured; the amplitudes are now 6-9 deg of tilt and 13-22 px of hop.
- **Optimising against the review's bbox-normalised difference metric.** Rejected,
  and worth recording: cropping to the alpha bbox deletes translation, so that
  metric is blind to the hop by construction. It answers "is this another drawing",
  which for a single-illustration pet is honestly "no". The claims this pull request
  makes are instead checkable: byte-duplicate count, per-state motion travel, the
  cell size, and the text.
- **Committing the frame generator and its master PNG into the repository.**
  Rejected for now. No built-in pet keeps its generator here (the one precedent,
  blue-throated-bee-eater, is under docs/archive), and this repository owns the
  TypeScript contract, not the art toolchain. The generator and master stay with
  the author; the shipped frames are the artifact of record, and the generator is
  offered to the reviewer on request.

## Consequences

- jyn-q is the first built-in pet whose artwork is declared as AI-generated in
  `THIRD_PARTY_NOTICES.md`, with the file's own AIGC label as the evidence. Future
  AI-art submissions should cite the embedded label rather than an assertion.
- The preview GIFs are now an evidence channel as well as a convenience: the intake
  gate wants images in the pull request body, not merely files in the repository.
- Regenerating these frames is not reproducible from a clone: the generator and the
  486x640 master live with the author. If a future change needs new frames, ask for
  them rather than trying to re-derive them from `build_jyn_q.py`'s absence.
- The byte-identical check in `tests/jyn-q-pet.spec.ts` is the reusable part: it
  catches "the loop repeats itself" mechanically, which is what a frame-level review
  of any single-illustration pet would otherwise have to catch by eye.
- The resolution figures are now single-sourced: the README row (both languages),
  this note, the cell comment in `tests/jyn-q-pet.spec.ts` and
  `assets/jyn-q/README.md` all carry the measured 254-383 px / median 331 px /
  ~0.74x numbers. `README.i18n.yaml` was re-recorded from the LF blobs at the same
  time; the earlier record had hashed CRLF copies of both READMEs and so matched no
  committed blob.
