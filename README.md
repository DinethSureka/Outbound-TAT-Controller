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
- **Stamp** — whatever is on the board carries the name of whoever put it there and
  the time they did, on the board and on the wall display.
- **Shared board** — optional. Off, each screen keeps its own copy. On, the extract
  and the store master live in one place: whoever uploads writes them, and every
  other open board is handed the change and redraws itself. No refresh, and nobody
  re-sends the file.

The board runs in the browser. Rules, settings and the documents you mark complete
are always kept in that browser's storage and go nowhere else. The extract itself is
read locally too — and stays local unless you turn the shared board on, which is the
one thing here that sends data anywhere.

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
4. **Upload** → drop in the picking extract. Columns are matched by header name, so
   extra columns and a different order are fine. The first upload on a screen asks
   who you are; that name is then stamped on everything you put up from it.
5. **Display** → put it on the wall screen.
6. Sharing across screens? See below — otherwise each screen keeps its own copy and
   the extract has to be dropped on each one.

To change the policy later, edit it under **TAT logic**, press **Export JSON**,
and put the new file on the shared drive for the other screens to pick up.

Posting to a Teams webhook only works when the file is opened from your own
machine; in the hosted view the browser blocks it, so use **Copy message** there.

## Sharing one board across screens

Without this, the board is a file: what you upload on your PC is not on anyone
else's. Turning sharing on gives the screens one picture between them.

It runs on **Firebase Firestore**, on Google's free plan. Nothing about the
connection is in this repository — it is entered once per screen, or imported from a
small JSON kept on the shared drive, and remembered there afterwards.

**Setting the project up — once:**

1. At `console.firebase.google.com`, **Create a project**. Analytics off.
2. **Build › Firestore Database › Create database**, region nearest the warehouse,
   **production mode**.
3. On the project home, the **web** icon (`</>`) registers an app. Copy its
   `projectId` and `apiKey`.
4. **Firestore › Rules** → publish:

   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /boards/{board}/{rest=**} {
         allow read, write: if true;
       }
     }
   }
   ```

**Then, on the board:** click the pill in the source bar → **Shared board** → paste
the two values, press **Make me a board key**, **Connect**. **Export connection
file** puts a `tat-shared-board.json` on the shared drive; every other screen
imports it and is set up in one click.

**What those rules mean.** They put no login in front of the data: anyone who knows
the project ID *and* the board key can read and write it. The board key is therefore
the lock — long, random, and kept off the internet, like the key to a shared drive
folder. The extract holds document numbers, stores and timings, so keep it to the
team. If the operation needs more, the same board runs behind Firebase
Authentication with a rule of `if request.auth != null`.

A board uploading a few times a day sits well inside the free Spark plan, and no
card is needed to stay on it. A screen that loses the network keeps working on the
data it has and catches up on its own when the connection returns.

TAT rules are **not** shared this way — they are policy, and still travel as the
`picking-tat-logic.json` export.

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
