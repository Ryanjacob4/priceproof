# PriceProof

**The store charged you more than the shelf said.** Snap the shelf tags and your receipt. PriceProof reads both with Gemini, matches every line, and flags:

- **Overcharges**: register price higher than the tag
- **Expired sale tags**: sale sign still up, full price charged
- **Deals not applied**: Buy 1 Get 1, "2 for $5"
- **Double scans**: same item rung up twice
- **Food recalls**: checks each item against the FDA's recall database (openFDA)

Then it writes the refund request for the service desk and a complaint for NJ's Office of Weights and Measures.

## Run it

1. Get a free Gemini API key at https://aistudio.google.com
2. Serve the folder (live scanning doesn't work inside the Claude preview):
   ```
   npx serve .
   ```
   or push to GitHub and turn on **GitHub Pages**, so judges can open it on their phones.
3. Open the app, tap **Settings**, paste the key, and save.
4. Tap **Check a real receipt**.

No build step. Everything is in `index.html`.

## Files

| File | What it is |
|---|---|
| `index.html` | The whole app (HTML + CSS + JS) |
| `demo-props.html` | Printable fake shelf tags + receipt for judging |

## How it works

1. **Read**: one Gemini call reads all tag photos plus the receipt and returns JSON.
2. **Match** (our own code, no AI): receipts shorten names like `RB BBY SPNCH 5Z`. The matcher:
   - turns words into "consonant skeletons" (`SPINACH` → `SPNCH`), which is how stores abbreviate
   - matches brand initials (`RB` → River Bend)
   - parses and compares sizes (`5Z` = 5 oz, `1G` = 1 gallon)
3. **Check**: the posted price (sale price if a sale tag is up), deal math, duplicates.
4. **Recalls**: openFDA food enforcement search, last 12 months. It only flags a recall when the **brand** matches, to avoid scaring people with generic "spinach" recalls.

## Judging demo (2 minutes)

1. Open with: *"NJ inspectors found Dollar General overcharging 2,000+ times across 58 stores. They paid $1.2M. Who checks every line of their receipt?"*
2. Hand the judge the printed props. They snap the tags and the receipt.
3. Result: **$3.59 overcharged**, a missed BOGO, an expired sale tag, a double scan, and a recall warning.
4. Show the matcher: point at `RB BBY SPNCH 5Z` matched to "River Bend Baby Spinach."
5. Tap **I only bought one**, and the total updates. Show the complaint, ready to send.

**Backup:** if Wi-Fi dies, tap **Use the sample receipt**. It runs fully offline.

## Team split

- **Person 1 (frontend):** polish, phone camera flow, add a store "report card" map
- **Person 2 (AI):** tune the Gemini prompt on real receipts from 3–4 different stores
- **Person 3 (matcher + recalls):** test on real receipts, grow the abbreviation list
- **Person 4 (pitch):** visit a store near campus, check 30–50 items, put real numbers in the pitch, record a backup demo video, write the Devpost page

## Notes

- The API key lives in the browser only. For a real launch, move the Gemini call to a small server.
- All sample and prop data is fictional and labeled DEMO.
- Sources: [NJ Attorney General on the Dollar General settlement](https://njoag.gov/?p=121908), [openFDA food enforcement API](https://open.fda.gov/apis/food/enforcement/).
