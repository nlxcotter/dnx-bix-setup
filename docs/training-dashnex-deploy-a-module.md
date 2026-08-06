# Training Module: Installing a module into a DashNex app and deploying it live

> **Author:** nlx-agency PM session. **Captured:** 2026-08-03, live, while installing Next Level Xcavate into the Next Level X Agency app and deploying it to `nextlevelxagency.com`. **Status:** draft, for GM curation. **Bucket:** open question, see the end of this doc.
>
> **Written to the GNAT-Standard** (`canon/gnat-standard.md`). Every step names its anchor, names the type of thing you are clicking or typing, and says what it is for.
>
> **Who this is for:** someone who has a DashNex Business app already pulled to their computer and a module they want to add to it. You do not need to know what any of these commands do. You need to be able to copy a line, paste it into a Terminal window, and press Return.
>
> **How long:** about twenty minutes if nothing goes wrong. **Two of the steps below cannot be done by anybody except the account owner**, and both of them stop everything until they are done. They are called out where they land.

---

## What you are about to do, in one paragraph

A DashNex module is a folder of ready-made features. You drop that folder inside your app, run one command that wires it in, install the supporting pieces, check it works on your own machine, and then push it up to DashNex and switch it on. **Nothing you do on your own machine touches your live website until the very last step**, so you can take as long as you like on the early parts.

---

# PART A: Before you touch anything

## A1. Confirm which business you are signed in to

**This is the step before every step.** DashNex signs you in per-folder, not globally. If you own more than one business, **the folder you are standing in decides which company your work lands on**, and there is nothing on screen to remind you.

**Open the Terminal app.** On a Mac, hold the **Command** key and tap the **Space bar**, type `terminal`, and press **Return**. A window with a blinking cursor opens. That window is where every command in this guide gets pasted.

**Move into your app's folder.** Type `cd `, leave one space after it, then drag your app folder from a Finder window onto the Terminal window and press **Return**. Dragging fills in the address for you **so that you do not have to type a long path correctly.**

**Now copy this line, paste it in, and press Return:**

```
npx dashnex whoami
```

**You will see a spinner for a few seconds, then a line naming a business**, like `You are logged in as Next Level X Agency`.

**Read that name and make sure it is the business you meant.** If it names a different company, stop here. You are in the wrong folder and everything after this would be built on the wrong account.

---

## A2. Know what a module can switch on without asking

Every module carries a small settings file called `dashnex.json`. **One line in it can start a scheduled job on your account that runs by itself, every day, and spends money.**

**Look at that file before you install anything.** In the Terminal window, paste this and press **Return**, changing `xcavate` to the name of your module folder:

```
cat packages/xcavate/dashnex.json
```

**You are looking for a section called `"schedules"`.** If it lists something like `"0 11 * * *"`, that module wants to run a job automatically once a day.

**If you see one, decide now whether you want it**, because it switches itself on the moment you deploy. To turn it off, change that section to read `"schedules": []` and save the file. **Empty brackets mean "no scheduled jobs," so nothing can start running on its own without you having chosen it.**

> **A real trap, and it is the reason this section exists.** On a different app, this same schedule had been switched off by editing that app's own settings. That switch-off **stayed with that one app and did not travel with the module.** Installing the module somewhere fresh turned the job straight back on. **If you want a scheduled job dead, it has to die in the module's own file**, or your next install quietly revives it.

---

# PART B: Getting the module into your app

## B1. Make the folder that modules live in

**In the Terminal window, paste this and press Return:**

```
mkdir -p packages
```

**Nothing appears on screen. That is correct**, this command is silent when it works. It creates a folder called `packages` inside your app, **so that there is a standard place for the module to sit where the app knows to look.**

## B2. Put the module inside it

**Drag the module folder into `packages`** using Finder, so the finished path reads `packages/xcavate`. If your module came as a ZIP file, unzip it first and drag the unzipped folder in.

**Do not rename the folder.** The wiring in the next step reads the folder's name to find the module, **so a renamed folder breaks the connection.**

## B3. Run the module's own installer

**In the Terminal window, paste this and press Return**, changing `xcavate` to your module's folder name:

```
node packages/xcavate/install.mjs
```

**You will see four lines with checkmarks**, roughly like this:

```
✓ Linked @dashnex/xcavate → "link:packages/xcavate"
✓ Added host dependency @react-pdf/renderer → "^4.5.1"
✓ Un-ignored packages/xcavate (excluding its node_modules) in .gitignore
✓ Added zero-flash theme script to src/app/layout.tsx
```

**Those four lines are the module introducing itself to your app.** It registers its own name, adds anything extra it needs, makes sure it will actually be uploaded when you push, and adds a small piece to your page so the module's screens do not flash white when they first load.

**If any line begins with a bullet rather than a checkmark**, like `• @dashnex/xcavate already linked`, that is fine. It means that piece was already done and it skipped it.

---

# PART C: Installing the supporting pieces

**This is where the only real wall in the whole process sits.** Two things go wrong here, both of them look like a disaster, and both are one line to fix.

## C1. The wall: the normal install command fails

If you reach for the usual command, `npm install`, **you get a red error and nothing installs:**

```
npm error code EUNSUPPORTEDPROTOCOL
npm error Unsupported URL Type "link:": link:packages/xcavate
```

**You have not broken anything and your app is fine.** DashNex modules describe themselves using a format that `npm` does not understand. The tool that does understand it is called **pnpm**, and it is the one DashNex is built around anyway.

## C2. Install pnpm

**In the Terminal window, paste this and press Return:**

```
npm install -g pnpm
```

**You will see a short line saying `added 1 package`.** That is all it does, it puts the correct tool on your machine **so that the next command can read the module's format.**

## C3. Install everything

**In the Terminal window, paste this and press Return:**

```
pnpm install
```

**A long list of package names scrolls past.** Ignore all of it, it is an inventory, not an error.

**At the very end you will most likely see this in red:**

```
[ERR_PNPM_IGNORED_BUILDS] Ignored build scripts: core-js-pure, esbuild, sharp, workerd
Run "pnpm approve-builds" to pick which dependencies should be allowed to run scripts.
```

**Do not panic and do not run the command it suggests.** This is a safety feature, not a failure. pnpm refuses to let downloaded packages run their own setup steps until you say which ones are allowed.

**But you cannot skip it either.** Two of those four, `esbuild` and `workerd`, use that setup step to download the pieces that actually build and run your site. **Leave them blocked and your site will not build.**

## C4. Allow the four setup steps

**Create a file called `pnpm-workspace.yaml` in your app's main folder** (the same folder that contains `package.json`), and put exactly this in it:

```yaml
allowBuilds:
  core-js-pure: true
  esbuild: true
  sharp: true
  workerd: true
```

**Then run the install again.** In the Terminal window, paste this and press **Return**:

```
pnpm install
```

**This time the four names appear with the word `Done` after them**, and the red block is gone. That means the pieces your site needs to build have actually downloaded.

> **The wrong fix, so you do not lose an hour to it.** Almost every answer online tells you to add a section called `onlyBuiltDependencies` inside `package.json`. **That was the old location and it silently does nothing now.** You get the identical red message back and no clue why. The setting moved into `pnpm-workspace.yaml` under the name `allowBuilds`.

---

# PART D: Proving it works before you touch your live site

**Everything so far happened only on your own machine.** These three checks tell you whether it will work before anyone on the internet can see it.

## D1. Build it

**In the Terminal window, paste this and press Return:**

```
pnpm build
```

**About halfway through, a wall of yellow technical text appears** mentioning things like `fontkit` and `IMPORT_IS_UNDEFINED`. **Ignore every line of it.** Those are notes, not errors, and they appear on a healthy build.

**What you are waiting for is the last few lines:**

```
✓ built in 1.80s
Build complete.
```

**If you see `Build complete`, the module compiled into your app.** If you see the word `error` in red near the end instead, the build genuinely failed and nothing further in this guide will work until it passes.

## D2. Check the module's pages and routes registered

**In the Terminal window, paste this and press Return:**

```
npx dashnex router
```

**A long numbered list of web addresses scrolls past.** Look for lines belonging to your module. For Xcavate those look like `POST /api/scan` and `GET /api/scans/mine`.

**Seeing them here means your app now knows the module exists**, which is the thing you actually installed it to achieve.

## D3. Create the module's storage on your own machine

**In the Terminal window, paste this and press Return:**

```
npx dashnex db migrate
```

**A list of file names scrolls past, most of them ending `.sql`.** At the end you want to see your module's own entries, like `xcavate:0001_init.sql`.

**If it says `All migrations are already applied`, that is a success, not a skip.** It means the storage your module needs already exists.

**This command only touches your own machine.** Your live site is untouched, **so you can run it as many times as you like without consequence.**

---

# PART E: Going live

## E1. Look at what is about to be uploaded

**In the Terminal window, paste this and press Return:**

```
git status
```

**You get a list of every file that has changed** since the last time you saved your work.

**Read it.** A deploy carries everything that is sitting in your app folder, not only the thing you were working on. **Checking this list first is how you avoid pushing a half-finished experiment live alongside the thing you meant to ship.**

## E2. Upload the app to DashNex

**In the Terminal window, paste this and press Return:**

```
npx dashnex app push
```

**You will see `All checks passed.` and then `Application pushed successfully.`**

**Then it asks you a question:** `? Deploy the application? (Y/n)`

**This has uploaded your files but has not switched anything on yet.** Your live site is still running the old version. You can answer this prompt, or press **Control** and **C** together to leave it and deploy separately in the next step.

## E3. Deploy it, and meet the gate only you can open

**In the Terminal window, paste this and press Return:**

```
npx dashnex app deploy
```

**★ If your app sells anything, this is where it stops**, and it stops for a reason no command can solve:

```
All checks passed.
You need to accept Master Payment Processing Agreement first by visiting this link:
https://business.dashnex.com/money/agreement/
```

**Nothing has changed on your live site.** It refused before doing anything, so there is no half-finished state to clean up.

**This is a legal agreement tied to your business account and only the account owner can accept it.** No developer, no assistant, and no command line can do this on your behalf, **so if someone else is doing your setup, this is the moment they have to stop and wait for you.**

**Click the link above**, which opens the agreement page in your browser. Read it and accept it. **You should land on a page headed with the agreement text and an accept control at the bottom.** If you land on a login screen instead, sign in and click the link a second time.

**Then come back to the Terminal window, paste the deploy command again, and press Return:**

```
npx dashnex app deploy
```

**This time you get two lines:**

```
All checks passed.
Application is deployed to yourdomain.com
```

**That second line is the moment it went live.** Your storage is created and your database is updated as part of this step, which is why there is no separate command for either.

---

# PART F: Checking it actually worked

**★ This part exists because of a mistake that is very easy to make and very hard to notice.**

## F1. Why "the page loaded" proves nothing

A DashNex app builds its pages in the visitor's browser rather than on the server. **That means every single web address on your site returns a successful "page found" response, including addresses that do not exist.**

Typing a nonsense address like `yourdomain.com/asdfghjkl` returns exactly the same successful response, at almost exactly the same size, as a real page. **So checking that a page "loads" tells you nothing whatsoever.** It is a test that cannot fail, which makes it a test that cannot pass either.

## F2. What to check instead

**Open your module's page in a browser and look at it with your own eyes.** That is the honest test, and it is the one that caught this. A page reporting success to a command-line tool was showing a plain `404 Page Not Found` to an actual human.

**Then check a working address that only the module could answer.** For Xcavate, asking for a scan that does not exist returns:

```
{"success":false,"error":"Scan not found"}
```

**That specific sentence is the proof**, because only the module could have written it. It means the module is deployed, running, and reading its storage successfully.

**And check a protected address.** An admin address answering `403` means the module is there and guarding itself. **An address answering `404` means the module never made it.** The difference between those two numbers is the difference between installed and absent.

## F3. Find the real address of your module's pages

**Do not trust the module's own instructions on this.** The Xcavate install guide said the front page would be at `/scan`. The real address is `/xcavate/scan`. Following the module's own documentation landed on a 404 and cost a genuine half hour of hunting.

**Get the true list from the router.** In the Terminal window, paste this and press **Return**:

```
npx dashnex router
```

**Look for the section listing pages rather than the ones beginning `/api/`.** Those are the addresses a visitor can actually type.

---

# PART G: The last thing, which is also yours alone

## G1. Enter your provider keys

**Most modules that talk to outside services deliberately refuse to work until you enter the keys yourself.** There is no fallback and no default. **That is on purpose, so that a freshly installed app can never quietly start spending money on somebody else's account.**

**Sign in to your live site as an administrator.** Then, **in the left-hand sidebar, click the entry named after your module** (for Xcavate it reads `Next Level Xcavate`).

**Along the top of that page is a row of tabs**, reading `Scoring fine-tuning`, `Location coverage`, `Styling`, `Report`, `Access`, `Scans`, `API`. **Click the tab on the far right of that row, labelled `API`.**

**You will now see a stack of boxes, one per key**, each with a label above it and a coloured word on the right reading either `Configured` or `Not set`.

**Paste each key into the box under its own label and click the `Save` button to the right of that box.** Each key saves on its own, **so you can enter one now and the rest later without losing anything.**

**The coloured word on the right changes from `Not set` to `Configured` when it has saved.** That word is your confirmation. There is no other message.

## G2. Which Google services to switch on

Xcavate needs exactly two, and it is worth knowing which **so that you are not switching on services you will be billed for and never use.**

- **Places API (New).** Finds the business and its details. **The word "New" matters**, there is an older service with almost the same name that this does not use.
- **Geocoding API.** Turns the town a visitor types into map coordinates so the search covers the right area. **Without this one the module refuses to run at all** rather than searching the wrong part of the country.

**In the Google Cloud console the buttons show what will happen if you click them, not what is currently on.** A service showing a `Disable` button is already enabled. A service showing an `Enable` button is currently off. **This reads backwards to most people the first time.**

---

# PART H: The provider keys, on the provider's side

**Covered in Part G is pasting the keys in. This is getting them in the first place.**

**Xcavate needs exactly two Google services switched on**, and knowing which saves you enabling things you get billed for and never use.

- **Places API (New).** Finds the business and returns its details. **The word "New" matters**, because an older service with almost the same name exists and this does not use it.
- **Geocoding API.** Turns the town a visitor types into map coordinates, **so that** the search covers the right area instead of the whole country. **Without this one the module refuses to run at all** rather than searching the wrong place.

**In the Google Cloud console, the buttons show what will happen if you click them, not what is currently on.** A service showing a **Disable** button is already enabled. A service showing an **Enable** button is off. **This reads backwards to almost everyone the first time.**

**If your key is restricted to specific APIs, both of these have to be on that list**, or calls fail with a permission error even though the services themselves are enabled.

---

# PART I: What you are actually building, THREE products and SIX IDs

**Read this before you create anything**, because the shape decides how many times you repeat the next four steps.

The module sells three separate things. **Each one needs its own Product, its own Offer and its own Checkout**, and each hands the app two IDs.

| # | What it sells | Quota variable | Reset period | Where the IDs go |
|---|---|---|---|---|
| 1 | The Xcavate full report | `max_reports` | **None**, one-time | `FULL_REPORT_PRODUCT_ID` + `FULL_REPORT_CHECKOUT_ID` |
| 2 | X-Ray, a one-time pack of runs | `max_xray_runs` | **None**, fixed pack | `onetimeProductId` + `onetimeCheckoutId` |
| 3 | X-Ray, a monthly subscription | `max_xray_runs` | **Monthly**, refills | `monthlyProductId` + `monthlyCheckoutId` |

**★ Read row 2 and row 3 again. Products 2 and 3 use the SAME quota variable name.** The only thing that makes one a subscription and the other a one-off pack is **the reset period**. That is the entire subscription mechanism: same quota, no reset means a fixed pack, monthly reset means it refills.

**Every ID starts blank, and blank means that tier's sales are simply off.** No ID pasted, button disabled, nothing sold. **A half-configured install cannot sell something it is unable to deliver**, which is deliberate and is the one thing here that fails safely.

---

# PART J: Building each product, offer and checkout

**Repeat all four steps below three times, once per row of the table above.**

## J1. The Product

**In DashNex, go to Products in the left sidebar, then click the Add product button.**

- **Name it.** Internal only, no buyer sees it.
- **Set Product type to Software.** Required, **so that** the product can carry a usage limit, which is what a credit is. Any other type cannot.
- **In the Variables box, type the quota name in snake_case.** `max_reports` for the report product, `max_xray_runs` for **both** X-Ray products.
- **⚠️ TRAP: you must press Return to lock the variable in.** On a desktop screen the field commits **only** on Return, because the plus button beside it is hidden on anything wider than a phone. **Typing the word and moving on does nothing, and the Create button will not even light up.** You know it took when the word jumps into a rounded chip and the box empties.
- Description and Tag can stay blank. **Click Create product.**

## J2. The Variant, where the quota gets its value

**On the product's row, click the ⋮ menu, choose Manage Variants, then click Add variant.**

- **⚠️ TRAP: the variable's NAME lives on the Product, but its VALUE lives on the Variant.** Two screens for one idea, and this is where most people stall, **so** expect it rather than hunting for a value field on the product screen.
- **Name the variant and click Create. Then reopen it** via its ⋮ menu and **Edit**. A **Variables** section now appears showing your quota name.
- **Set the value.** That number is how many credits one purchase grants.
- **★ Set the Reset Period. This single field is the only thing separating a subscription from a pack.**
  - Report product: **None**
  - X-Ray one-time: **None**, a fixed pack of runs
  - X-Ray monthly: **Monthly**, so the quota refills each period, which is the thing the subscriber is paying for
- **Good news, and it removes something you would otherwise have to hold in your head.** As of 2026-08-04 the module's settings screen **enforces this**. If you paste the wrong X-Ray product into the wrong slot, **it tells you which of the two products you actually pasted and refuses to save.** You do not have to remember which ID was which.
- **The one case it cannot judge, stated rather than hidden:** if a single product declares the runs quota **twice**, once refilling and once not, across different variants, it satisfies either slot and passes both. That is honest, because a product built that way genuinely could serve either tier. **The guard stops the confusable pair, not every possible misconfiguration.**
- **Click Update variant.**
- **If the Variables section is missing entirely**, the variable never stuck on the product. Go back and redo the Return trap in J1.
- **Copy the Product ID now.** Products list, the row's ⋮ menu, **Copy ID**. Paste it somewhere you can find it, **so that** you are not hunting for six IDs at the end.

## J3. The Offer

**In DashNex, go to Offers in the left sidebar and create one.**

- **Internal name** is yours alone. **Invoice name** appears on the buyer's receipt and on their card statement, **so make it something they will recognise six weeks later** rather than disputing it.
- **External description shows at checkout and is customer-facing copy**, not a note to yourself.
- **Pricing:** price type, currency, price. **For the monthly X-Ray this is where the recurring price lives, and it has to agree with the Monthly reset period you set on the variant.** A monthly price against a `None` reset sells a subscription that never refills.
- **Items, then the ＋ Add item button, then pick your Product and its Variant.** **This link is what makes the money actually deliver the thing.** Skip it and the customer pays and receives nothing.
- **Guarantee** is optional here and is configured properly on the checkout.
- **Click Create.**

## J4. The Checkout

**Open the offer you just made and go to its Checkouts section.**

- **DashNex auto-creates a default checkout** with Express pay already switched on. **For a simple one-time product that is genuinely enough.**
- **⚠️ TRAP: there is no "Copy ID" for a checkout.** The row's ⋮ menu offers only **Preview** and **Copy link**. Click **Copy link**, then take **only the last segment of that address**, the part after `/checkouts/`. **Not the whole link.**

---

# PART K: The custom checkout, where the bump lives

**★ NOT YET CAPTURED. The four items at the end of this doc name exactly what is missing.**

**This is where a real funnel diverges from the simple case.** A default checkout was not used here. **A custom checkout was built from scratch and the order bump is the point of it**, not a later add-on.

**What is known from the form itself**, captured while it was on screen: internal name, three timer modes, payment methods, express checkout and billing-address and phone toggles, custom invoice name, main offer pricing with a strikethrough, bump settings with their own pricing, four custom tag fields, a guarantee block with text and refund method, the look-and-feel block holding the featured graphic and all bullet copy, bump box colours, sales terms, footer disclaimer, and discounts.

**What is not known and will not be invented:** which screen it is created from, what was entered in each field, where the bump is configured, whether the bump needs its own Offer and Product or attaches to an existing one, and what the customer sees in order from click to receipt.

---

# PART L and M: Images and descriptions

**★ NOT YET CAPTURED.**

Where product and offer images are uploaded, what dimensions survive, which descriptions are customer-facing rather than internal, and where each one actually surfaces on the buying page.

**Open question, deliberately not decided:** these may not be steps at all. They may be fields met three separate times inside J and K. **How the admin presents them settles it**, so no shape is being forced in advance.

---

# PART N: Connecting the six IDs, and the two things that will not catch you

**In your live app, sign in as an administrator and open the module's settings from the left sidebar.** Paste each Product ID and each Checkout ID into its matching field and save.

**Each field validates on Save**, so a typo is caught by you rather than by a customer at the till:
- A bad product ID returns **"No product found with that ID."**
- A product missing its quota returns **"That product has no `max_reports` quota, pick the report product."**
- A bad checkout ID returns **"No checkout found with that ID."**
- The quota may live on the product **or** on any of its variants. Both are accepted.

**Both fields flip to Configured and the button goes live.**

## ★ Two blind spots on this screen, and both are money paths

**Blind spot one: the checkout check confirms the checkout EXISTS. It does not check what it SELLS.** Straight from the code that runs it:

> *"Confirm the checkout ID is a real checkout (catches a typo'd/invalid ID). We deliberately do NOT inspect what the checkout's offer sells, pairing the right checkout to the report product is the admin's call."*

**So a real, valid checkout ID that sells the wrong thing saves clean and shows Configured.**

**Blind spot two: FIXED 2026-08-04, and recorded here because the reason it existed is the lesson.**

The product check used to prove only that a quota with the **right name** was present. **Both X-Ray products carry the same name, `max_xray_runs`**, and the reset period, the only field telling the two tiers apart, was never read. **The two Product IDs were interchangeable and both slots saved green**, which cost money in both directions: a subscriber billed monthly against a quota that never refilled, or a single payment refilling forever.

**The screen now reads the reset period and judges each product against the slot it is being pasted into.** Paste the wrong one and it names which product you actually pasted and refuses to save. **Tested against both crossed cases before shipping.**

**Why it is left written down instead of quietly deleted:** the check that failed was the one that *looked* strict. An operator who had just been refused for a missing quota reasonably concluded the screen was checking their work, and it then waved through the most confusable pair in the whole configuration. **A validation that is right about the easy case and silent about the hard one is worse than no validation, because it buys trust it has not earned.**

## ★ Therefore, and this is a required step rather than a suggestion

**Six green fields is still not proof that anything works**, even with the tier guard in place. **The guard covers the products. Nothing covers the checkouts**, and blind spot one above is unchanged and deliberate: a real checkout ID selling the wrong thing saves clean.

**Before you send a single human being to your site, buy each of the three tiers yourself.** Real card, real checkout, all the way to the receipt. **Then confirm the credit actually landed in the account.**

**That is the only test that means anything**, and it is twenty minutes against a failure that charges people and delivers nothing while telling you everything is fine.

**What changed and what did not, so this step is not misread as optional now:**
- **Products: guarded.** The wrong X-Ray product in the wrong slot is refused at save.
- **Checkouts: not guarded, by design.** Pairing a checkout to the right tier is still entirely yours, and nothing will catch you.
- **So test-buy is still required.** It is now the mitigation for the checkout gap rather than for both.

---

# ★ The four things still missing, and only the account owner has them

1. **The custom checkout form, field by field**
2. **The bump configuration**, including whether it needs its own Offer and Product
3. **Images and descriptions**, where they live and where they surface
4. **What failed on the first attempt**, anywhere in Parts I to M. **No artifact holds this**, and it is the part a stranger following this guide dies on.

---

# The gotchas, collected

**Migrations on the live database.** There is a command that looks like it updates your live database directly, `dashnex db migrate --remote`. **It will fail** with `CLOUDFLARE_API_TOKEN environment variable is required`, and you almost certainly do not have that token. **You do not need it.** The live database is updated as part of `dashnex app deploy`.

**Running two DashNex apps at once on your own machine.** Each app is configured to run at the same local address. **Start a second one while the first is running and the second will generate links pointing at the first**, which produces blank pages that look like a broken build. Run one at a time.

**The module's own install guide can be wrong.** Verify page addresses against `dashnex router` rather than against the documentation, **because the router is generated from the code that actually runs.**

---

# ★ Gap questions, honestly listed

**These are the points where this guide is written from inference rather than from something I watched happen.** Each one needs a yes or no before this doc is safe to teach from.

**1. Does the deploy really run the live database migrations?** The module's storage exists on the live site and I never ran a migration against it, so the deploy must have done it. **I inferred that from the result rather than seeing it happen.** Is that how DashNex describes it?

**2. Was the payment agreement triggered by this module, or does every DashNex deploy hit it?** The agency business had never sold anything before. **I do not know whether adding a module that takes payments caused the gate, or whether any first deploy would have.** This changes whether the gate belongs in a general guide or only in a selling-module guide.

**3. Did the storage bucket actually get created?** The module asks for a storage area called `SCAN_STORAGE`. **I never confirmed it exists on the live account.** It is visible to you in the DashNex dashboard and not to me.

**4. Did any scheduled job end up switched on?** I emptied the schedule in the module's file before pushing, so it should not have. **I have not seen the deployed configuration with my own eyes**, only what I sent.

**5. Why did one key already show `Configured` on a brand-new app?** The GenX key read as configured on the agency app before it was entered there. **The module has no fallback anywhere in its code**, so a value must exist in that app's storage. I cannot explain it and it is worth explaining before this doc claims keys always start empty.

**6. Which bucket does this doc belong in?** It teaches an owner to get their own app live, which sounds client-facing. **But it also contains package-manager troubleshooting that a non-technical owner would never meet**, because their assistant would hit it first. **Client-teachable, PM-teachable, or split into two?**
