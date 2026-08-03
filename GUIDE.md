# Connect with Bayezid — full setup guide

Two separate things in this zip, going to **two separate GitHub repos**:

| Folder | Goes to | Why separate |
|---|---|---|
| `site/` | Your **existing** `sayadbayezid-portfolio-` repo | It's already wired up for GitHub Pages + your custom domain — this replaces the old design in place, it doesn't need a new repo. |
| `worker/` | A **new** repo (e.g. `bayezid-agency-worker`) | This is a Cloudflare Worker (backend API), a completely different kind of project from a static site — it needs its own deploy pipeline. |

Do the **worker first** — the site's JavaScript already points at its URL (reviews, contact form, Pixel forwarding), so nothing on the site actually works end-to-end until the worker is live.

---

## Part 1 — Deploy the worker (new repo)

1. Create a new GitHub repo, e.g. `bayezid-agency-worker`.
2. Push everything inside `worker/` to the **root** of that repo (not inside a subfolder — `src/`, `wrangler.jsonc`, `.github/`, etc. all sit at the top level).
3. Repo → Settings → Secrets and variables → Actions → add:
   - `CLOUDFLARE_API_TOKEN`
   - `CLOUDFLARE_ACCOUNT_ID`
   (same values you already used for SmartLeadGen — reuse them, no need for new ones)
4. Push to `main`. GitHub Actions deploys automatically — watch the "Actions" tab.
5. Once the first deploy succeeds, set the Meta secret:
   ```
   npx wrangler secret put META_CONVERSIONS_API_TOKEN
   ```
   (paste the Conversions API token — generate a **fresh** one from Events Manager if the one from earlier screenshots was ever pasted anywhere public)
6. Confirm the worker is live by visiting:
   `https://bayezid-agency-api.sayadmdbayezidhosan.workers.dev/api/reviews`
   — it should return `{"reviews":[]}` (empty is correct — no reviews approved yet).

## Part 2 — Update the site (existing repo)

1. In your `sayadbayezid-portfolio-` repo, replace whatever's currently there with everything inside `site/` — same folder shape: `index.html`, `services.html`, `products.html`, `work.html`, `about.html`, `contact.html`, and `assets/style.css` + `assets/main.js`.
2. Check for a `CNAME` file in the repo (should just contain `sayadbayezid.com`). If it's not there, add it — without it, GitHub Pages serves this at `bayzed123.github.io/sayadbayezid-portfolio-/` instead of your real domain.
3. Push. GitHub Pages redeploys automatically — no Actions workflow needed for a static site.
4. Add your real photo at `site/assets/bayezid-portrait.jpg` whenever you have one — the hero avatar shows a placeholder until then.

## Still genuinely not done (not being skipped — just honestly out of scope for one pass)

- **Blog system** — needs its own content model and post pages, a real separate project.
- **Newsletter** — needs an email provider decision first (Mailchimp, Resend, etc.) before it can be wired up.
- **Case studies beyond SmartGen/SmartLeadGen** — add more as real client work happens.
- **Reviewing/approving submitted reviews** — no admin UI yet; ask me to check the `bayezid-agency` D1 database and I'll approve/list them directly, the same way I've been managing SmartLeadGen's access codes.
- **Contact form submissions** — same as above: stored in D1, no inbox/notification yet. Ask me to check `contact_submissions` any time, or say the word and I'll wire up an email notification.
