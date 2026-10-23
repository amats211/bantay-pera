# Bantay Pera — notes for AI editors (Cursor)

Personal + business money tracker (Personal, FoneKingdom repair shop, Confident Tech).
Installable PWA hosted on GitHub Pages: https://amats211.github.io/bantay-pera/
Single-file app: all HTML, CSS and JS live in `index.html`. No build step, no frameworks.

## Files
- `index.html` — the whole app
- `manifest.json` — install info (name, icons, colors)
- `sw.js` — offline support (network first, falls back to cache)
- `icon-*.png` — app icons

## Rules that protect saved data (do not break)
- Storage key is `bantay-pera-v1`. Never rename it.
- User data lives in the global `S` object, saved to localStorage by `save()`.
- Never rename or remove existing fields in `S`, `S.tx`, `S.accounts`, `S.debts`, `S.bills`, `S.techs`.
  Add new fields with defaults instead.
- Any change to the data shape needs a matching step in `migrate()` so old saved data and
  old backup files still load.
- Restore points use keys `bantay-pera-snaps` (index) and `snap:<id>`. Keep them compatible.
- Account balances are computed: `opening` + entries. Never store a balance directly.

## Data model (short)
- `tx` entries: `{id, date 'YYYY-MM-DD', type 'in'|'out'|'xfer', amt, book, cat, acct, to?, note,
  job?{tech,price,parts,comm}, pay?{tech,day}, debt?}`
- `acct` is an account id, or `card:<debtId>` for a credit card purchase (adds to that card's balance).
- `book`: 'me' (Personal), 'fk' (FoneKingdom), 'ct' (Confident Tech), or a custom id.
- `debts`: kind 'card' (balance, rate %/month, min) or 'inst' (monthly, term, paidMonths).
- `bills`: freq 'monthly'|'quarterly'|'yearly', start month 1–12, due day.
- `paid[YYYY-MM][itemId] = {tx, amt}` marks a bill/debt paid for that month.

## Shop pay rules
- Lester: 40% cut. ₱300 no-repair pay is added manually when paying (rule 'manual').
- Eder: 20% cut + ₱200 every day (rule 'always').
- Owner's own jobs: no cut. Cut base is set in Settings (price minus parts by default).

## Style
- Mobile first (~380px). Colors are CSS variables in `:root` with a dark-mode block; reuse them.
- Plain wording for a Filipino user; amounts in pesos via `peso()`.

## Before pushing
1. Test locally with a local server (not by double-clicking the file).
2. If you changed `sw.js` or the cached file list, bump `CACHE` in `sw.js`.
3. Commit with a short message, then push. GitHub Pages updates in about a minute.
