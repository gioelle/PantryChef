# Optional store notes

Tips for common US grocery stores. These are **examples**, not defaults: the user chooses their own stores during setup, and only the notes for their enabled stores matter. Sites change; if a note no longer matches what you see, trust the site and tell the user.

Stores to offer during setup (pick the ones that make sense; the user can name any other):
Aldi, Whole Foods / Amazon Fresh, Kroger (and its banners), Target, Walmart, Costco, a local store on Instacart, Trader Joe's (in-store only).

Suggested ids for `settings.json` → `stores.enabled`: `aldi`, `whole_foods`, `amazon_fresh`, `kroger`, `target`, `walmart`, `costco`, `instacart_<store>`, `trader_joes`.

## Aldi
- Online ordering at aldi.us is fulfilled through Instacart (it can also be shopped on instacart.com). Pickup and delivery both exist; online prices may be marked up vs in store, and Instacart adds fees and tips. Mention this when comparing.
- Store brands are usually the cheapest equivalents.
- Adding items: search via `aldi.us/store/aldi/s?k=<query>`, click the product's **Add** button, **wait ~3 seconds**, and confirm it turned into a **"1 ct" stepper** before navigating away. Navigating too soon silently drops the add. Clicking by element reference was unreliable; clicking by **screenshot coordinates** worked (use the x,y exactly as they appear in the returned screenshot).
- **"Request"** instead of Add means out of stock at that store; record it in `availability`.
- Selection is smaller; specialty items are often unavailable. Record misses so they route to another store next time.

## Whole Foods Market / Amazon Fresh
- Ordered through Amazon (the Whole Foods storefront on amazon.com, or Amazon Fresh), signed in with the user's Amazon account. Pickup and delivery depend on location and membership; read what the site shows, don't assume.
- 365 by Whole Foods Market is the cheapest house brand.
- Whole Foods and Amazon Fresh carts are separate; make sure you're adding to the one the user chose.

## Kroger (and banners like Ralphs, Fred Meyer, King Soopers, Harris Teeter)
- Ordered on the banner's own site; the user picks a store for pickup or delivery. Prices and availability are per store, so confirm the store is set first.
- Digital coupons must be "clipped" to the account; you may point out relevant ones but leave clipping to the user unless they ask.

## Target
- target.com with Drive Up / Order Pickup or Same Day Delivery (Shipt). Confirm the selected store before adding groceries; many fresh items are pickup/delivery only.

## Walmart
- walmart.com grocery pickup or delivery; confirm the selected store and that you're adding the pickup/delivery version, not a shipped item from a third-party seller.

## Costco
- Same-day grocery on costco.com is delivered through Instacart; bulk sizes. Compare unit price, but check the quantity is realistic for the household before choosing bulk perishables.

## Local stores on Instacart
- instacart.com, choose the specific store. Prices can be higher than in store; fees and tips apply. The Aldi add-to-cart tips above generally apply to Instacart storefronts too.

## Trader Joe's
- **In-store only**: no online ordering or cart. Put its items on a grouped checklist in the plan file and set `online_cart: false` in `stores.details`.
