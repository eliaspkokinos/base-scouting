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

### 4. Update the results count
- Increment the `results-count` on the position page by 1
- `reports.html` count updates dynamically via JS — just add the row, no manual count needed there

### 5. Commit and push
```
git pull origin main --no-rebase --no-edit && git add . && git commit -m "add [Player Name]" && git push
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

## Current players

| Player | File | players.json | handcrafted |
|---|---|---|---|
| Matej Deket | `reports/matej-deket.html` (generated) | ✓ full entry | ✓ |
| Ridwan Popoola | `reports/ridwan-popoola-final-report.html` | ✓ full entry | ✓ |
| Vasilije Novičić | `reports/vasilije-novicic-report.html` | ✓ full entry | ✓ |
| Hamza Güreler | `reports/hamza-gureler-report.html` | ✓ minimal entry | ✓ |
| Adem Avdić | `reports/adem-avdic.html` | ✓ minimal entry | ✓ |
| Andrei Borza | `reports/andrei-borza.html` | ✓ minimal entry | ✓ |

Position pages: `defenders.html` (6 cards), `midfielders.html` (7 cards), `attackers.html`

---

## Key rules

- **`handcrafted: true` on every entry** — build.js skips these, preserving hand-crafted HTML
- **Photos must use GitHub raw CDN URLs** — relative paths don't resolve correctly on Netlify
- **Never use `--rebase`** on git pull — use `--no-rebase`
- **DNA 4th category** in reports is labeled "Watch Closely / Needs Development" → maps to `dna.exposed` in JSON
- **build.js** uses defensive `?.` and `?? []` throughout — added after a Netlify crash from a partial entry

---

## What the user provides
- The report HTML file (`firstname-lastname-report.html`)
- Position (defender / midfielder / attacker)
- Month and year of the report
- Images already uploaded to GitHub (`images/` and `heatmaps/` folders)
