# PantryChef

A [Claude Code](https://claude.com/claude-code) skill that turns photos of your pantry, fridge and freezer into a kitchen inventory, picks real recipes from the internet that fit your preferences and use what you already have, and builds grocery carts that keep your kitchen stocked, including your staples.

Run `/pantry-chef` once a week and it will:

1. **Read your kitchen.** Send photos of your cabinets, pantry, fridge and freezer. It lists what it sees, asks about anything it's unsure of, and saves the inventory.
2. **Catch up on last week.** It asks which planned recipes you actually made, and how much (½×, ¾×, 1×, 2× or a custom amount). Then it subtracts the ingredients, tracks leftovers, and confirms that any order you placed arrived.
3. **Plan meals.** It finds recipes from reputable, human-written recipe sites and test kitchens, **never AI-generated or invented**. It favors ingredients that are about to go bad and fits the plan to your diet, allergies, equipment and goals. You get nightly recipes or a batch-prep plan of components to mix and match.
4. **Keep staples stocked.** Routine meals (your usual breakfast, a rotating lunch) and always-on-hand items (eggs, milk, coffee) are checked against what you have and topped up.
5. **Build a short shopping list.** It removes what you already have and basics like salt and oil, merges duplicates, and rounds to real package sizes.
6. **Pick a store per item.** It uses your stated or learned preference first, then the better unit price at the quality level you chose (cheapest, balanced or higher quality, overall or per category).
7. **Fill your carts.** It uses the Claude in Chrome extension, in your own Chrome where you're signed in to your stores. It adds the items, then **stops at checkout so you can review the cart and place the order yourself.**

Over time it learns brands you insist on, items it should always ask you about, items never to restock, which store is out of what, and which recipes you liked.

## Requirements

| Need | Why |
|---|---|
| **Claude Code** with skills (CLI or desktop app) | Runs the skill |
| **Web search and web fetch** tools | Finds and reads recipe pages |
| A model that can **read images** | Reads your kitchen photos |
| **Claude in Chrome** extension, connected to Claude Code, and you **signed in to your grocery store accounts** in that Chrome | Searches your stores and fills real carts. Without it you still get the meal plan and shopping list, and you fill the carts yourself. |
| *Optional:* **Google Drive for desktop** or **iCloud Drive** | A photo inbox folder you can upload to from your phone |

## Install

```bash
git clone https://github.com/gioelle/PantryChef.git
```

```bash
mkdir -p ~/.claude/skills && cp -R PantryChef/skills/pantry-chef ~/.claude/skills/
```

Then restart Claude Code (or open a new session) and run:

```
/pantry-chef
```

Without git: on GitHub choose **Code → Download ZIP**, unzip it, and copy the `skills/pantry-chef` folder into `~/.claude/skills/`. You should end up with `~/.claude/skills/pantry-chef/SKILL.md`.

> **Already have your own `pantry-chef` skill?** Don't copy this over it. Both use the name `pantry-chef` and the same default data folder (`~/PantryChef/`), so this would replace your customized skill. Back yours up first, or install this one under a different folder name and change `name:` in its `SKILL.md`.

### Updating

Pull the latest version and copy the skill folder again. Your data lives in `~/PantryChef/`, separate from the skill, so updating doesn't touch it.

```bash
cd PantryChef && git pull && cp -R skills/pantry-chef ~/.claude/skills/
```

## First run

The first time you run `/pantry-chef`, it introduces itself: what it does, how the weekly loop works, what it needs, and its safety rules. Then it walks you through a short setup:

- **Household and meals:** how many people, and which meals to plan (dinners, lunches, breakfasts, snacks).
- **Cooking style:** cook most nights, batch-prep and reheat, or a mix, plus equipment like an air fryer or slow cooker and a weeknight time limit.
- **Photo intake:**
  - a local inbox folder;
  - a synced Google Drive or iCloud folder you upload to from your phone;
  - or no folder, and you drop photos into the chat.

  With a folder, you choose whether used photos go to the Trash or into a `processed/` subfolder.
- **Goals**, any you like:
  - **Nutrition:** calorie cap, high protein, specific macros, lower carb or keto.
  - **Eating pattern:** plant-forward, vegetarian or vegan, Mediterranean, heart-healthy or low sodium.
  - **Health:** high fiber, gut health, low added sugar, more whole foods.
  - **Practical:** budget-focused, kid-friendly, quick, minimal dishes.
- **Diet:** allergies, restrictions, dislikes, cuisines you love or avoid, proteins you prefer or avoid.
- **Staples:** meals you always want stocked, items to always keep on hand, optional treats, and basics you always have.
- **Stores:** any stores you like, pickup, delivery or ask each week, and brand rules ("always this brand", "always ask me", "don't restock").
- **Cheaper or higher quality:** overall and per category (e.g. organic produce, premium coffee, store-brand pantry goods).

Change anything later by saying "change my settings", "add a staple", "add a store" and so on.

## Everyday commands

| Say | What happens |
|---|---|
| `/pantry-chef` or "plan my meals" | The full weekly run |
| "Here are my fridge photos" | Updates the inventory only |
| "What can I make tonight?" | One recipe from what you have, no shopping |
| "Build my carts" | Fills carts from the latest plan |
| "I cooked X" / "We used the last of Y" / "I bought Z" | Updates the inventory |
| "Add X to next week's order" | Queues an item for the next shopping list |
| "I prefer X from store Y" / "Never restock Z" | Teaches it your store and brand rules |

## Stores

You choose your own stores. Nothing is built in as a default. [`store-notes.md`](skills/pantry-chef/reference/store-notes.md) has tips for common US stores: Aldi (Instacart), Whole Foods and Amazon Fresh, Kroger, Target, Walmart, Costco and local Instacart stores. Trader Joe's is in-store only, so it gets a printable checklist instead of a cart. For any other store, it works out the site on the first run.

## Safety

- It fills carts up to the checkout review page and **never places the order**. That click is always yours.
- It **never types passwords, card numbers or payment details.** If a site needs you to sign in, it stops and asks you to.
- It **asks before removing** anything already in your cart.
- Photos it has used are only **moved to the Trash** (or a `processed/` folder). They are never permanently deleted.
- Recipes are always linked to their source; it summarizes and never copies full instructions.

## Privacy

- Everything it learns is plain JSON and Markdown in your data folder (`~/PantryChef/` by default) on your own computer. That includes inventory, goals, preferences, prices and weekly plans.
- Browser actions happen in **your own Chrome**, under your own store accounts.
- The skill doesn't upload your data folder anywhere. Recipe searches go to the web like any search.

## Your data folder

```
~/PantryChef/
  settings.json        household, goals, diet, photo intake, stores, brands, budget/quality
  staples.json         staple meals, always-on-hand items, optional treats
  inventory.json       what's in the kitchen, orders on the way, items queued for next time
  stores.json          learned preferences, products, prices, availability
  recipes.json         planned, cooked, liked and rejected recipes
  photos/inbox/        new photos (if you chose a local inbox)
  plans/YYYY-MM-DD.md  each week's plan, shopping list and cart summary
```

Field-by-field details are in [`schemas.md`](skills/pantry-chef/reference/schemas.md).

## Repository layout

```
skills/pantry-chef/
  SKILL.md                            the skill: first-run intro, setup, weekly loop
  reference/inventory-from-photos.md  reading photos, the photo inbox, moving photos to Trash
  reference/recipe-sources.md         trusted recipe sites, not-AI checks, scoring
  reference/store-ordering.md         choosing stores and products, cart workflow, learning rules
  reference/store-notes.md            optional tips for common US grocery stores
  reference/schemas.md                data file reference
  templates/*.json                    empty starting files copied into your data folder
```

## Troubleshooting

- **`/pantry-chef` isn't recognized:** check that `~/.claude/skills/pantry-chef/SKILL.md` exists, then restart Claude Code.
- **It can't reach the store sites:** make sure the Claude in Chrome extension is installed and connected, and that you're signed in to the store in that Chrome.
- **Photos in a Google Drive or iCloud folder can't be read:** open the folder once in Finder so the files download, then run again.
- **Start over:** move `~/PantryChef/` to the Trash (or rename it) and run `/pantry-chef`. It will run the first-time setup again.
