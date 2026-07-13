# B.A.S.E. Scouting — Project Guide for Claude

## What this project is
A static scouting site deployed on Netlify from the GitHub repo `eliaspkokinos/base-scouting` (main branch). Node.js `build.js` reads `players.json` and generates report HTML files into `reports/`. Netlify runs `node build.js` on every push.

---

## File naming convention
All report files: `firstname-lastname-report.html` (e.g. `vasilije-novicic-report.html`)
All player photos: `images/firstname-lastname.png`
All heatmaps: `heatmaps/firstname-lastname-heatmap.png`
Never change a filename after it's been linked — it breaks all hrefs.

---

## When a new report HTML is added

Do these steps in order:

### 1. Add to `players.json`
- Read the report HTML in full
- Extract every piece of content: bio, scores, role, behaviors, DNA, profiles, devPriorities, travelScore, leagueScores, riskGauges, trajectoryNote, nowAge, verdict
- Add a **complete** entry matching the exact schema of existing entries
- Always include `"handcrafted": true` — this tells build.js to skip the file and preserve the hand-crafted HTML
- **A missing field will crash the Netlify build. Do not do bio-only entries.**

### 2. Add a card to the correct position page
- Defenders → `defenders.html`
- Midfielders → `midfielders.html`
- Attackers → `attackers.html`
- Match the HTML structure of existing cards exactly
- Use the GitHub raw CDN URL for photos: `https://raw.githubusercontent.com/eliaspkokinos/base-scouting/main/images/firstname-lastname.png`
- Set correct `data-role`, `data-potential`, `data-age`, `data-date` attributes
- Age = current age at time of report; date format = `YYYY-MM`

### 3. Add a row to `reports.html`
- Insert in the correct month section (create the section if it doesn't exist yet)
- Use `data-date="YYYY-MM-DD"`, `data-nation`, `data-position`, `data-potential`, `data-name`
- Same GitHub raw CDN photo URL
- **Run the pre-push checklist below before committing**

### 4. Update the results count
- Increment the `results-count` on the position page by 1
- Increment the `stat-item-num` (report count) in `reports.html` stats panel manually — it is a static string
- `reports.html` results-count text updates dynamically via JS on page load — no manual edit needed there
- **Also increment the `stat-num` hero stat in `index.html`** — static string, does not auto-update

### 5. Pre-push checklist — run these before every commit

**In `reports.html` — the new `.report-row`:**

- [ ] `.row-club` text is ordered **Role · Club**, not Club · Role
  - Correct: `🇭🇺 Complete Forward · Ferencváros`
  - Wrong:   `🇭🇺 Ferencváros · Complete Forward`
  - Reason: ellipsis truncation at mobile widths drops the end of the string; role must survive, club can be cut
- [ ] The wrapper div (second child of `.report-row`, containing `.row-name` + `.row-club`) has no extra nesting and no inline `style` or `min-width` override — the CSS rule `.report-row > div:nth-child(2){min-width:0}` in the ≤900px and ≤540px blocks handles it automatically
- [ ] `.row-photo-wrap` has no inline `width` or `height` — base CSS sets 72px and mobile CSS overrides to 56px/48px; any inline style breaks Safari grid layout

**In the new report file (`reports/*.html`):**

- [ ] The file contains the canonical `@media(max-width:900px){...}` mobile block inside its `<style>` tag — copy it verbatim from `reports/vasilije-novicic-report.html` if creating a new file from scratch; every report file needs this block for nav, hero, sections, footer

**Counts (static strings, must be updated manually):**

- [ ] `reports.html` stats panel `<div class="stat-item-num">` (report count) incremented by 1
- [ ] `index.html` `.stat-num` hero stat incremented by 1

### 6. Commit and push
```
git pull origin main --no-rebase --no-edit && git add . && git commit -m "add [Player Name] — [Position], [Club], [Month Year]" && git push
```
- Always pull before committing — the user sometimes uploads images directly on GitHub
- Never use `--rebase`
- `git config http.postBuffer 524288000` may be needed if pushing large images

---

## players.json schema
```json
{
  "slug": "firstname-lastname",
  "handcrafted": true,
  "firstName": "",
  "lastName": "",
  "positionTag": "",
  "positionCategory": "Defenders | Midfielders | Attackers",
  "photoUrl": "https://raw.githubusercontent.com/eliaspkokinos/base-scouting/main/images/firstname-lastname.png",
  "photoAlt": "",
  "verdictLine": "",
  "bio": {
    "dob": "", "nationality": "", "club": "", "league": "",
    "contractExpiry": "", "marketValue": "", "preferredFoot": "", "height": "", "agent": ""
  },
  "scores": { "basePotential": "", "travelReadiness": "", "reportDate": "" },
  "role": {
    "primaryRole": "", "description": [],
    "formationName": "", "formationLabelBottom": "",
    "formationPositions": [{ "cx": 0, "cy": 0, "label": "" }],
    "highlightedPosition": { "cx": 0, "cy": 0, "label": "" },
    "heatmapZones": [], "heatmapEllipses": []
  },
  "behaviors": { "onBall": [{ "title": "", "text": "" }], "offBall": [{ "title": "", "text": "" }] },
  "dna": {
    "pure": [{ "name": "", "desc": "" }],
    "context": [{ "name": "", "desc": "" }],
    "system": [{ "name": "", "desc": "" }],
    "exposed": [{ "name": "", "desc": "" }]
  },
  "profiles": {
    "athletic": [{ "label": "", "value": "" }],
    "cognitive": [{ "label": "", "value": "" }],
    "psychological": [{ "label": "", "value": "" }]
  },
  "devPriorities": [{ "num": "01", "title": "", "text": "" }],
  "travelScore": { "score": "", "prose": [] },
  "leagueScores": [{ "league": "", "score": 0, "width": "0%", "high": false, "note": "" }],
  "riskGauges": [{ "score": 0, "label": "", "meaning": "", "note": "" }],
  "trajectoryNote": "",
  "nowAge": 0,
  "verdict": {
    "statement": "",
    "travels": [],
    "targeted": [],
    "potentialRating": "",
    "potentialBalls": 0,
    "potentialProjection": ""
  }
}
```

---

## Current state (as of July 2026)

- **32 reports** live in `reports.html`
- **9 countries** covered
- Stats panel in `reports.html`: `<div class="stat-item-num">32</div>` (update manually)
- Hero stat in `index.html`: `<div class="stat-num">32+</div>` (update manually)
- All 32 report files have the canonical mobile block applied
- All 32 `.row-club` entries use Role · Club order

---

## Key rules

- **`handcrafted: true` on every entry** — build.js skips these, preserving hand-crafted HTML
- **Photos must use GitHub raw CDN URLs** — relative paths don't resolve correctly on Netlify
- **Never use `--rebase`** on git pull — use `--no-rebase`
- **DNA 4th category** in reports is labeled "Watch Closely / Needs Development" → maps to `dna.exposed` in JSON
- **build.js** uses defensive `?.` and `?? []` throughout — added after a Netlify crash from a partial entry

---

## What the user provides when adding a new player

The user will say something like: "Add [Name], [position group], [report date], [brief profile]."
That is enough to proceed through all six steps above. Do not ask for more.

- Report name / slug (inferred from name if not given)
- Position group: defender / midfielder / attacker
- Date the report was done (month + year)
- Brief profile (club, role, nationality) — enough to write the `.row-club` and `data-*` attributes
- Images already uploaded to GitHub (`images/` folder) before the session starts
