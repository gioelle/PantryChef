# Store selection and cart building

The user's stores live in `settings.json` → `stores.enabled` (any store, defined by the user). Store-specific tips are in `store-notes.md`; nothing else in the skill hardcodes a store.

## Browser
Use **Claude in Chrome** (the user's real Chrome, where they're signed in). Load its tools in one ToolSearch call before starting. If Chrome isn't connected, stop and tell the user how to connect it rather than switching to another browser (which has none of their sign-ins). Without it, finish the plan and the per-store shopping list so the user can shop themselves.

## Choosing products
1. `preference.brand_specific: true` → only an `approved_brands` product; if none is available, ask the user. Never substitute on your own.
2. Learned or stated product in `stores.json` → search for it by name and brand and add it.
3. Otherwise search the generic name, filter to the right size range and quality level, and pick the lowest **unit price**. Quality level comes from `settings.json` → `budget_quality`:
   - `cheapest`: store brand or cheapest acceptable equivalent.
   - `balanced`: best unit price among reasonably reviewed mid-tier products; store brands are fine.
   - `quality`: favor higher-quality or premium lines (organic, pasture-raised, small-batch) when the price gap is reasonable; still compare unit price within that tier.
   - `per_category` entries override the overall setting (e.g. `"produce": "organic"`, `"coffee": "quality"`, `"pantry": "cheapest"`). `quality.organic` lists categories that must be organic.
4. Quantity: enough to cover the list amount with the fewest packages.
5. Record each pick as an observation in `stores.json` (product, brand, size, price, unit price, URL, date, in_stock).

## Price comparison when there's no preference
- Compare unit price for equivalent products at the same quality level. A quick check of a second store is worth it for expensive items (meat, fish, cheese, nuts, olive oil, coffee); for cheap items under ~$3, use the stored price or default to the store already getting more items.
- Online prices can be marked up compared with in store, and delivery services add fees, tips, or membership costs. Mention this when it changes the answer.
- Show the user: item, price at each store compared, pick.

## Cart workflow (per store)
1. Open the store, confirm the user is signed in and the right store location and pickup vs delivery mode are set (`settings.json` → `stores.fulfillment`; if `ask`, ask now).
2. Check the existing cart; list what's there and ask before removing anything.
3. Add items one at a time; verify each landed in the cart with the right quantity (see `store-notes.md` for sites where adds silently fail).
4. Open the cart, compare it against the list, fix mismatches.
5. Proceed only as far as the cart or checkout review page. Suggest a pickup or delivery window if the site asks, but let the user confirm it.
6. **Stop.** Do not click Place order / Pay / Submit. Do not enter payment details or passwords. Tell the user which tab is ready.

General browser tips that apply to most grocery sites:
- Prefer the site's search URL over typing into the search box when it's known.
- If clicking an element by reference doesn't register, take a screenshot and click by the coordinates shown in that screenshot.
- After clicking Add, wait a few seconds and confirm the button changed (to a quantity stepper or "in cart") before navigating away.

## Stores without an online cart
For in-store-only stores, write their items as a grouped checklist in the week's plan file (by store section) instead of building a cart.

## Learning rules (stores.json)
- User statement ("I like store X's ___", "only buy ___ at Y", "only brand Z") → `preference` with `source: "user"`. Highest priority, never overridden by price.
- "Always ask me about ___" → `preference.brand_specific: true` with `approved_brands` if they name any.
- "Don't restock ___" → `preference.do_not_restock: true`.
- User rejects a product → add it to `disliked_products` for that item.
- Out of stock or not carried → `availability` entry with date; after two misses in 60 days, treat as not carried there and route elsewhere.
- Repeated choice with no complaints (3+ orders) → `preference` with `source: "learned"`; mention it the first time it takes effect so the user can veto.

## Adding a store
Add its id to `settings.json` → `stores.enabled` and an entry in `stores.details` (display name, website, how ordering works, `online_cart` true/false). If `store-notes.md` has no section for it, figure out the flow on the first run (search URL, add button behavior, out-of-stock wording) and tell the user what you learned so they can keep it.
