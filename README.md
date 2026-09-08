# Outbound TAT Controller

Picking TAT control board for outbound warehouse operations — one self-contained
HTML file. Open it and it runs: no server, no build step, no install.

**Live board:** https://dinethsureka.github.io/Outbound-TAT-Controller/

## What it does

Reads a picking / OBD extract and a store cut-off master, works out when each
delivery is due to be packed, and shows what is about to breach.

- **Rules engine** — the first matching rule sets a document's TAT and where its
  clock starts (order stamp, a fixed cut-off, or the store's own cut-off calendar).
- **Store master** — the cut-off calendar is edited on the board: add or remove a
  site, set which weekdays it takes an order and at what code. Kept per browser,
  so it is loaded once rather than re-uploaded.
- **Status bands** — out of TAT, and three warning bands you set in hours or as a
  percentage of the TAT.
- **Working list** — ordered so whatever breaches next is at the top; anything
  already out of TAT collects into one block at the foot.
- **Display board** — a full-screen wall view that drives itself: paged urgent
  list, workload ring, breakdown, histogram, breach ticker and a live countdown
  to the next document due.
- **Alert** — a tone when a document crosses into out of TAT, with tone and
  volume settings.
- **Teams** — builds a message from what is on the board, to paste or post to a
  channel webhook.
- **Export** — the current view as CSV.

Everything runs in the browser. Files are read locally and never uploaded; rules,
settings and the documents you mark complete are kept in that browser's storage.

## Demo data, placeholder rules

The board opens with sixteen invented documents, twelve invented sites and an
illustrative set of TAT rules, so it is never empty for someone seeing it for the
first time. **None of it is real, and the rule numbers are not any operation's
actual commitments.** The demo stamps are generated relative to the current time,
so the board always shows a live spread across the status bands rather than a
frozen date.

Your own rules stay out of this repository. Export them once from a board that
has them, keep the JSON on your shared drive, and import it on each screen.

## Setting up a screen

1. Open the board — the link above, or the `.html` file from your shared drive.
2. **TAT logic → Import** → pick your `picking-tat-logic.json`. This replaces
   every rule, status band and cut-off code in one step, and the browser
   remembers it from then on. Do this once per screen.
3. **Edit stores** → load your cut-off master once, from the file or from a
   `store-master.json` export. It is then kept on that browser and edited in
   place — add a site, change a weekday cut-off, drop a site — so the file never
   needs uploading again.
4. **Upload** → drop in the picking extract.
   Columns are matched by header name, so extra columns and a different order
   are fine.
5. **Display** → put it on the wall screen.

To change the policy later, edit it under **TAT logic**, press **Export JSON**,
and put the new file on the shared drive for the other screens to pick up.

Posting to a Teams webhook only works when the file is opened from your own
machine; in the hosted view the browser blocks it, so use **Copy message** there.

## Files

| File | |
|---|---|
| `Picking-TAT-Dashboard.html` | The whole application. |
| `index.html` | Redirect so the Pages root opens the dashboard. |

Excel extracts and your exported rules are deliberately **not** tracked — see
`.gitignore`. They hold live operational data and policy, and belong on the
shared drive rather than in a repository.

## Updating

Edit `Picking-TAT-Dashboard.html`, commit, push. GitHub Pages republishes on its
own — there is nothing to build. Screens pick the change up on their next
refresh, and their imported rules and completion marks survive it.

---

Developed by **Dineth Chandrasiri**.
