# How Doxxable Are You?

A self-check privacy scanner that shows **how an attacker would chain your public clues into an account takeover** and lets you break the chain.

Enter **your own** email or username. You get a 0-100 Doxx Score, an animated **attacker's path** diagram (leaked data, linked accounts and public code feeding a targeted phish and then a takeover), and a fix plan. Tick a fix and the matching link snaps, the score drops, and you earn XP.

**Nothing you type is stored or logged.** Only XP and badges are kept, in your own browser.

## What makes it different
Breach checkers such as Have I Been Pwned and Mozilla Monitor list what leaked. This project:
- turns findings into one **score** and a **prioritized fix plan**,
- shows the **attacker's path** and which single fix breaks it,
- adds **missions, XP and badges** so people finish the fixes,
- is **open source and plug-in based**: a new check is one small file (see CONTRIBUTING.md).

## Quick start
```
npm install
cp .env.example .env     # optional: add a free GITHUB_TOKEN (no scopes) to avoid GitHub rate limits
npm start
```
Open the address printed in the terminal (usually http://localhost:3000; it moves to the next port if 3000 is busy). Use **Use sample data** to see everything with no internet. Run `npm test` for the automated checks.

## Features
- **Identity scan**: breach exposure (XposedOrNot), username on 15 platforms, email in public GitHub commits.
- **Attacker's path**: animated chain diagram that updates as you fix risks.
- **Password check**: SHA-1 hashed in your browser, only a 5-character prefix goes to Pwned Passwords.
- **Link and QR check**: follows redirects server-side (with SSRF protection) and flags shorteners, raw IPs, punycode and long chains.
- **Spot the scam**: 8-message quiz with streaks.
- **Score card**: download a PNG with only your score and rank, never your email or username.

## How the score works
Breaches: +8 each (max 5), +15 if sensitive data such as passwords was exposed. Usernames: +2 per platform (max 20). Public GitHub email: +15. Total capped at 100. Low 0-30, Medium 31-60, High 61-100. A check that fails to run adds 0 points and shows "Couldn't check".

## Deploying
**Full app (recommended): Render.** The repo includes `render.yaml`. In Render choose New, then Blueprint, pick this repo, and add `GITHUB_TOKEN` when asked. Or create a Web Service manually: build command `npm install`, start command `npm start`. Free instances can sleep when idle, so open the site before presenting.

**Static demo: GitHub Pages.** The `docs/` folder is a static copy. Set Settings, Pages, Source to "Deploy from a branch", branch `main`, folder `/docs`. Sample data, the attacker's path, the quiz and the password check work there; live scans and link tracing need the server. After changing anything in `public/`, run `npm run build:docs` and commit `docs/`.

**Single file.** `index.html` at the repo root is the whole front end in one file (run `npm run build:single` to rebuild after editing `public/`). Double-click it, or set Pages to branch `main`, folder `/ (root)`.

**CI.** `.github/workflows/ci.yml` runs `npm ci` and `npm test` on every push.

## API
- `POST /api/scan` `{email?, username?}` returns `{score, level, label, results[], topFixes[]}`
- `POST /api/unroll` `{url}` returns the redirect chain and warning flags
- `GET /api/demo`, `GET /api/health`

## Honest limits
- Third-party sites change. Username detection is best effort: a site that blocks us counts as inconclusive, never as found.
- Breach data comes from third-party services and may be incomplete.
- Without `GITHUB_TOKEN`, GitHub allows very few requests per hour per IP.
- XP and badges are stored locally and can be edited by the user. They are for fun, not security.
- Only scan identifiers you own. This tool is for privacy awareness, not for looking up other people.

## Contributing
New checks are tiny plug-in files. See [CONTRIBUTING.md](CONTRIBUTING.md). MIT licensed.

## Credits
Built on XposedOrNot, Pwned Passwords (Have I Been Pwned) and GitHub's public API.
