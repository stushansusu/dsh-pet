# jyn-q review evidence (PR #13)

Review evidence for the jyn-q pull request, captured from the **running** DSH Web GUI
(`http://127.0.0.1:19387`) and from the shipped frames. It is review material, not pet
content: nothing here is read by the plugin, and `docs/` is outside `package.json`'s
`files` whitelist, so none of it is published. Drop the directory before merge if you
would rather not carry it.

| File | What it shows |
| --- | --- |
| `jyn-q-picker.png` | Settings > 宠物, the pet selector displaying 鲸鱼娘·Q版. See the caveat below. |
| `jyn-q-states.webp` | Every track sampled left to right — what each state actually looks like. |
| `jyn-q-all-states.gif` | All eight tracks played in the pet's own frame cadence, one after another. |
| `jyn-q-before-after.webp` | Top: the frames the 2026-10-12 review saw. Bottom: the frames now shipped. |
| `picker-options.txt` | The selector's option list, read out of the live DOM unmodified. |

## How these were captured

`jyn-q-picker.png` and `picker-options.txt` come from a Chromium session driven with
Playwright, authenticated with the same browser-session cookie the shell mints
(`session cookie for authority 127.0.0.1:19387`). The option list is dumped straight
from `select#settings-pet-pet`, so it is the real list the running host serves.

**Caveat on the screenshot.** The selector is a native `<select>`, whose open dropdown
is browser chrome that cannot be captured. To make the closed control display the new
pet in one frame, the page's own `select.value` is written through the native setter
and then **verified** (`select displaying at capture time: 鲸鱼娘·Q版`); no `change`
event is dispatched, so nothing is staged, persisted or selected — the live pet stayed
the maintainer's own `whale-girl` throughout. The unmodified evidence for "the picker
lists jyn-q" is `picker-options.txt`, not the screenshot.

`jyn-q-states.webp`, `jyn-q-all-states.gif` and `jyn-q-before-after.webp` are rendered
from `assets/jyn-q/frames/` by the author's generator-side scripts, not from the GUI.

## One thing a live capture cannot show yet

The host builds the pet registry at startup (`service.pets()`), so a *running* shell
keeps serving the manifest it read when it started — the picker row and the frames
follow the installed copy, but a freshly regenerated manifest does not appear until
`dsh web` is restarted. The frames in these images are the shipped ones.
