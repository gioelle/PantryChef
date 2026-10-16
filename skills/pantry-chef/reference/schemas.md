# Data file reference

All files carry `schema_version`. Dates are ISO `YYYY-MM-DD`. Item `name`s are lowercase generic names and are the join key across files (inventory ↔ staples ↔ recipes ↔ stores). Keep these shapes consistent so later runs (or other tools) can read them. Examples below are illustrative only.

## settings.json
- `setup_complete`: `false` until the setup questionnaire is done. While `false`, `/pantry-chef` runs the introduction and setup.
- `data_dir`: where all files live (default `~/PantryChef`).
- `units`: `us` | `metric`.
- `photo_intake.mode`: `chat` | `local_folder` | `synced_folder`. `inbox_path` is the folder to read (null for chat). `after_use`: `trash` | `processed` (null for chat). `provider` for synced folders: `google_drive` | `icloud` | `other`.
- `household.people`, `household.leftovers_ok`.
- `meals_per_week`: counts for `breakfasts`, `lunches`, `dinners`, `snacks` (0 = don't plan).
- `goals.selected`: any of `calorie_cap`, `high_protein`, `macro_targets`, `lower_carb`, `keto`, `plant_forward`, `vegetarian`, `vegan`, `mediterranean`, `heart_healthy`, `low_sodium`, `high_fiber`, `gut_health`, `low_added_sugar`, `whole_foods`, `budget`, `kid_friendly`, `quick`, `minimal_dishes`. `goals.notes` holds anything else in the user's words.
- `macro_goals.per_serving` / `macro_goals.daily`: numeric targets. `null` in any target means "no constraint". Starting points per serving to offer (confirm with the user, then store the real numbers):
  - high protein: ≤600 kcal, ≥35 g protein
  - balanced: ≤650 kcal, ≥25 g protein, ≤70 g carbs
  - lower carb: ≤600 kcal, ≥30 g protein, ≤30 g carbs
  - keto: ≤20–30 g net carbs per serving (and per day if tracked)
  - calorie cap: ≤500 kcal, ≥25 g protein
  - high fiber: ≥8 g fiber
  - heart-healthy / low sodium: ≤600 mg sodium, ≤5 g saturated fat
  - low added sugar: ≤6 g added sugar
- `diet.allergies`, `diet.restrictions`, `diet.dislikes`, `diet.proteins_avoided`, `diet.cuisines_avoided`: hard filters.
- `diet.protein_preferences`, `diet.cuisines_loved`: soft preferences that boost scores.
- `diet.meal_style.mode`: `nightly` | `batch_prep` | `mixed` | `assembly`. `prep_days` (e.g. `["sunday"]`), `equipment` (e.g. `["air fryer", "sheet pans"]`), `notes` (preferred dish types, reheating at work, etc.).
- `diet.weeknight_max_active_min`: time limit for nightly cooking.
- `budget_quality.overall`: `cheapest` | `balanced` | `quality`. `budget_quality.per_category`: `{ "<category>": "cheapest" | "balanced" | "quality" | "organic" }`, overrides the overall level.
- `quality.organic`: categories that must be organic (e.g. `["berries", "leafy greens"]`).
- `assumed_basics`: never added to shopping lists or deducted from inventory.
- `stores.enabled`: store ids in the user's order of preference. `stores.details["<id>"]`: `{ "name", "website", "ordering", "online_cart" }`. `stores.fulfillment`: `pickup` | `delivery` | `ask`. `stores.zip` and `stores.locations` are optional.
- `recipe_sources.preferred` / `blocked`: additions to and removals from the trusted list.

## staples.json
```json
{
  "id": "weekday-oats",
  "name": "Weekday overnight oats",
  "servings_per_week": 5,
  "per_person": false,
  "ingredients": [ { "name": "rolled oats", "amount": 50, "unit": "g", "preferred_product": null } ],
  "notes": ""
}
```
- `meals[].ingredients[].amount` is **per serving**. Weekly need = amount × `servings_per_week` (× `household.people` if `per_person`).
- A meal may have `rotation[]` (several variants, each `{ "name", "ingredients": [...] }`) instead of one `ingredients` list; spread `servings_per_week` across the variants as its `notes` say (notes can also say prepped proteins from the weekly plan count toward it).
- `always_on_hand[]`: `{ "name", "min_quantity", "unit" }`. Restock when inventory < `min_quantity`.
- `optional_treats[]`: `{ "name", "criteria", "frequency" }`. Offer on the shopping list as optional (never auto-add), and only pick products meeting `criteria`.

## inventory.json → items[]
```json
{
  "name": "plain greek yogurt",
  "brand": "Example Dairy",
  "product": "Plain Greek Yogurt 32 oz",
  "quantity": 16,
  "unit": "oz",
  "location": "fridge",
  "spot": "middle shelf",
  "confidence": "medium",
  "perishable": true,
  "use_by": "2026-10-24",
  "use_by_estimated": true,
  "kind": "ingredient",
  "servings": null,
  "recipe_id": null,
  "source": "photo",
  "last_seen": "2026-10-15",
  "pending_check": false,
  "notes": ""
}
```
- `kind`: `ingredient` | `leftover`. Leftovers set `servings` and `recipe_id`.
- `source`: `photo` | `user` | `order` | `leftover`.

## inventory.json → on_order
`null`, or an order the user said they placed and hasn't confirmed received:
```json
{ "store": "store_id", "placed_on": "2026-10-16", "expected": "2026-10-17", "items": [ { "name": "...", "product": "...", "quantity": 1, "unit": "ct" } ] }
```
At the start of the next run, ask whether it arrived and what was substituted or missing; then move the items into `items` (source `order`) and set `on_order` back to `null`. Several open orders may be kept as an array.

## inventory.json → shopping_queue[]
Items the user flagged to buy on the next run: `{ "name", "store", "product", "added", "reason" }`. Step 4 always includes them; clear them once they're in a cart the user checks out.

## recipes.json → recipes[]
```json
{
  "id": "site-sheet-pan-chicken-fajitas",
  "title": "Sheet Pan Chicken Fajitas",
  "site": "Example Recipe Site",
  "author": "Author Name",
  "url": "https://...",
  "servings": 4,
  "time_min": { "total": 40, "active": 15 },
  "macros_per_serving": { "calories": 420, "protein_g": 36, "carbs_g": 30, "fat_g": 16, "fiber_g": 5, "sodium_mg": null, "basis": "listed" },
  "ingredients": [ { "name": "chicken breast", "amount": 1.5, "unit": "lb", "optional": false } ],
  "role": "dinner",
  "status": "planned",
  "planned_for": "2026-10-15",
  "cooked_on": null,
  "multiplier": null,
  "rating": null,
  "history": []
}
```
- `role`: `dinner` | `lunch` | `breakfast` | `snack` | `component` (batch-prep building block).
- `status`: `planned` | `cooked` | `skipped` | `rejected`.
- `multiplier`: batch size actually made (0.5, 0.75, 1, 2, custom). Deduction = each ingredient `amount` × `multiplier`.
- When a recipe is cooked again later, push the prior `{cooked_on, multiplier, rating}` into `history` before overwriting.
- `macros_per_serving.basis`: `listed` (from source) | `estimated` (computed).

## stores.json → items{ "<name>": … }
```json
"peanut butter": {
  "preference": { "store": "store_id", "product": "Natural Creamy Peanut Butter 16 oz", "source": "user", "set_on": "2026-10-15" },
  "disliked_products": [ { "store": "store_id", "product": "...", "reason": "texture" } ],
  "availability": [ { "store": "store_id", "in_stock": false, "date": "2026-10-15" } ],
  "observations": [
    { "store": "store_id", "product": "...", "brand": "...", "size": "16 oz", "price": 3.49, "unit_price": 0.218, "unit": "oz", "url": "https://...", "date": "2026-10-15" }
  ],
  "notes": ""
}
```
- `preference.store: null` means a brand preference with no store preference: buy that product wherever it's cheaper.
- `preference.brand_specific: true` (with `rule` and `approved_brands[]`): never auto-pick this item; use an approved brand or ask the user.
- `preference.do_not_restock: true` (optionally with `rule`, e.g. "using up what's left"): keep the item off shopping lists even when inventory runs out.
- `preference.source`: `user` (stated, always wins) | `learned` (inferred after repeat choices; user can veto).
