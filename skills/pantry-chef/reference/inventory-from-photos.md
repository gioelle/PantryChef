# Reading inventory from photos

## Before reading
- One area per photo is easiest. If a photo mixes areas or the area is unclear, ask.
- Several photos of the same area (e.g. fridge door + shelves) are one snapshot — merge them, don't double count.
- If an image is too dark or blurry to read a shelf, say which shelf and ask for a retake rather than guessing.

## What to record per item
- `name`: plain generic name ("plain greek yogurt", "black beans, canned") — this is what recipes match against.
- `brand` and `product` if the label is readable (feeds store learning).
- `quantity` + `unit`: count sealed packages as packages with their size ("2 × 15 oz can"). For open containers estimate fill level ("~half of 32 oz tub" → 16 oz).
- `location`: cabinet / pantry / fridge / freezer (and `spot` like "door" if useful).
- `confidence`: high (label readable, quantity clear), medium (identifiable, quantity guessed), low (best guess at what it is).
- `perishable` and a rough `use_by` for fresh produce, meat, fish, dairy, leftovers. Base it on typical shelf life from today's date unless a date is visible; mark it `estimated`.

## Common traps
- Opaque containers and leftovers: record as "leftover container (unknown)" with low confidence and ask.
- Items behind items: don't invent them. The not-seen list covers things that were there last time.
- Condiments and spices: record them, but don't fuss over quantities — "have / low / out" is enough.
- Frozen bags: estimate weight from fullness.

## Merging into inventory.json
1. For each area photographed, mark that area's existing items as `pending_check`.
2. Match new items to existing ones by `name` (and brand). Matched → update quantity, `last_seen`, clear `pending_check`.
3. New items → add with `source: "photo"`.
4. Still `pending_check` → present as "not seen this time". User says keep → leave as is with a note; gone → remove.
5. Areas not photographed this time stay untouched.

## Confirmation table (show the user)
| Area | Item | Qty | Confidence | Note |
|---|---|---|---|---|

Only ask about the medium/low rows and the not-seen items; high-confidence items are just listed for review.

## Where photos come from
`settings.json` → `photo_intake`:
- `mode: "chat"`: photos arrive in the conversation. Nothing to file afterwards.
- `mode: "local_folder"` or `"synced_folder"`: read every image in `inbox_path` (jpg, jpeg, png, heic, webp; skip the `processed/` subfolder and hidden files). Synced folders (Google Drive for desktop, iCloud Drive) can hold placeholders that haven't downloaded yet; if an image can't be read, say which one and ask the user to open the folder once so it downloads, rather than skipping it silently. HEIC files may need converting first: on macOS, `sips -s format jpeg <in.heic> --out <scratch>/<name>.jpg` into a scratch folder, never into the inbox.

## After reading (only once inventory.json is saved and confirmed)
Handle every photo you used per `photo_intake.after_use`. Never use `rm`, `unlink`, `del`, "empty Trash", or any other permanent delete.
- `"trash"`: move each file to the system Trash so the user can still restore it.
  - macOS: `osascript -e 'tell application "Finder" to delete (POSIX file "/full/path/photo.jpg")'` (moves to Trash; works for Google Drive and iCloud folders too, which then show it in their own trash).
  - Linux: `gio trash "/full/path/photo.jpg"`.
  - Windows (PowerShell): `Add-Type -AssemblyName Microsoft.VisualBasic; [Microsoft.VisualBasic.FileIO.FileSystem]::DeleteFile('C:\full\path\photo.jpg','OnlyErrorDialogs','SendToRecycleBin')`.
  - If none of these work, fall back to `"processed"` and tell the user.
- `"processed"`: move each file to `<inbox_path>/processed/YYYY-MM-DD/` (create it if needed).
Tell the user how many photos were moved and where.
