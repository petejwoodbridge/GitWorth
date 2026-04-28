# GitWorth

<p align="center">
  <img src="hero.svg" alt="A vintage receipt-printer valuation certificate appraising the GitWorth repository itself: 12 stars, 0.0 years old, MIT license, line items totalling $475 with a wry appraiser's note." width="720" />
</p>

A single-file static web app that issues a tongue-in-cheek "appraisal" valuation of any public GitHub repository, styled as a vintage receipt-printer certificate.

## An early appraisal method for your repo

Most repository "metrics" tools wait until you have something to measure — thousands of stars, a contributor graph, a year of commits. GitWorth runs from day one. Drop a freshly-initialised project in and it returns a calibrated, line-itemed valuation: positive items for what's there (committed aesthetic, novel concept, a license file), deductions for what isn't (no contributors, no readme, stale tree), and a final number that reflects the appraiser's judgment rather than a sum of round thousands.

It's an entertainment piece — but unlike a star count, it gives a tiny early-stage repo *something to print on the receipt*.

## How it works

1. You enter an `owner/repo` slug.
2. The page hits the public GitHub API to collect real metrics: stars, forks, watchers, open issues, contributor count, recent commit cadence, age, language mix, license, README.
3. Those metrics plus a README excerpt are sent to Claude (Anthropic API) with a prompt asking for an itemised valuation in USD: line items, subtotal, final number, and a short appraiser's note.
4. The result renders as a printed receipt.

## Deploying to GitHub Pages

This is a single `index.html` with no build step.

1. Push the file to a repo (root, or `/docs`, or a `gh-pages` branch — whichever your Pages settings expect).
2. Settings → Pages → Source = your branch.
3. Done. Visit the URL.

## A note on API keys

There's no backend. Keys you enter live in `localStorage` in your browser and are sent directly from your browser to:

- `api.github.com` (optional token, raises rate limit)
- `api.anthropic.com` (required, the Claude call)

The site host (GitHub Pages) never sees them. If you publish this for others to use, **don't bake your own keys in** — let visitors paste their own. For a public-facing demo with shared keys you'd need a tiny proxy (Cloudflare Worker, Vercel function) — out of scope here.

The Anthropic call uses the `anthropic-dangerous-direct-browser-access` header, which is required for direct-from-browser calls. This is fine for a personal demo where each user supplies their own key.

## Customising the valuation logic

The system prompt for Claude is in [index.html](index.html) inside `getValuation()`. Tweak the rules (line item count, tone, calibration anchors) and the output JSON schema there. The schema is parsed strictly, so if you add fields, update `renderResult()` too.

## Caveats

- Contributor count is taken from page 1 of the contributors endpoint and capped at 30 — it's a signal, not an exact count.
- Commit cadence is sampled from the last 30 commits, not the full history.
- The valuation is speculative entertainment, not financial advice.
