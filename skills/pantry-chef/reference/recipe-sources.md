# Finding real recipes

The user wants recipes written and tested by real people at reputable publications or established blogs — **not AI-generated** recipes, and never one you write yourself.

## Trusted sources (start here)

Merge with `settings.json` → `recipe_sources.preferred` (the user's additions win) and skip anything in `recipe_sources.blocked`.

**Test-kitchen / editorial** (strongest): Serious Eats, Bon Appétit, Epicurious, Food & Wine, EatingWell, Simply Recipes, The Kitchn, Food Network (chef-attributed recipes), BBC Good Food, King Arthur Baking. Paywalled (use only if the user has access): NYT Cooking, America's Test Kitchen / Cook's Illustrated.

**Long-running individual blogs**: Budget Bytes, Smitten Kitchen, RecipeTin Eats, Once Upon a Chef, Cookie and Kate, Love and Lemons, Minimalist Baker, Pinch of Yum, Skinnytaste (good for macros), Downshiftology, Gimme Some Oven, Woks of Life, Just One Cookbook, Damn Delicious, Well Plated, Fit Foodie Finds, Ambitious Kitchen.

**Use with care**: Allrecipes (user-submitted — prefer recipes with many reviews and photos).

## Authenticity checks (reject if any fail)
- A named human author with a history on the site (byline, about page).
- The site predates widespread AI content (roughly before 2022), or it's an established publication.
- Real process photos, reader comments/reviews, or a test-kitchen note.
- Ingredient amounts and instructions are internally consistent (a "30-min" recipe that braises for 2 hours fails).
- Red flags: no author, generic stock images, filler intros with no specifics, dozens of near-identical recipes posted on the same day, "AI-assisted" disclosures, domains that are content farms or aggregators republishing others' recipes. Aggregators (e.g. Yummly, Pinterest) → follow through to the original source and evaluate that.

## Searching
- Lead with what's on hand: `chicken thighs zucchini feta recipe site:budgetbytes.com OR site:seriouseats.com OR site:recipetineats.com`.
- Add a goal angle when relevant: "high protein", "under 500 calories", "low carb", "vegetarian", "high fiber", "meal prep", "sheet pan", "air fryer".
- Run several searches in one turn across different ingredient combos, then fetch the 6–12 most promising pages.

## Reading a recipe page
Extract: title, author, site, URL, servings, total and active time, ingredient list with amounts, nutrition per serving if listed. Many sites embed schema.org `Recipe` JSON-LD — prefer that when present.

**Macros**: if the page lists nutrition, use it and mark `listed`. If not, estimate from the ingredients using standard USDA values and mark `estimated`. Never present an estimate as the source's number.

## Scoring (pick the best set, not just the best individual recipes)
| Factor | Weight | Notes |
|---|---|---|
| Uses on-hand ingredients | high | % of non-basic ingredients already in inventory, weighted toward perishables near use-by |
| Goal and macro fit | high | distance from per-serving targets in `settings.json` → `macro_goals`, plus fit with `goals.selected` (e.g. fiber, sodium, added sugar, plant-forward); hard limits (calorie cap, vegetarian, allergies) are filters, not scores |
| New items needed | medium | fewer is better; an item shared by two recipes counts once |
| Allergies / restrictions / dislikes / time / equipment | filter | must pass; `diet.proteins_avoided` and `diet.cuisines_avoided` are filters too |
| Protein and cuisine preference | medium | favor `diet.protein_preferences` and `diet.cuisines_loved` |
| Practical goals | medium | `quick`, `minimal_dishes`, `kid_friendly`, `budget` in `goals.selected` |
| Reheat fit (batch prep) | medium | when `diet.meal_style.mode` is `batch_prep`: holds up 4–5 days and reheats well in the user's equipment |
| Variety | medium | avoid repeating a recipe cooked in the last ~3 weeks, avoid the same main protein every night |
| User taste | medium | boost sources/cuisines the user 👍'd in `recipes.json`, demote 👎 |

## Copyright
Link the source. Show ingredients needed for the shopping list and a one-line description, never the full instructions.
