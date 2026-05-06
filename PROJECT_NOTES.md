# Duke's Dog Grooming — Project Notes

Static one-page marketing + booking site for Joe Wise's home-based dog grooming business in Tulsa, OK. Built as a low-cost (~$10/year), zero-maintenance site that handles booking via an embedded third-party scheduler.

Live at: **https://dukesdoggrooming.com**

---

## Current status

| Item | Status |
|---|---|
| Static site built and deployed | ✅ Live on GitHub Pages |
| Custom domain wired up | ✅ DNS propagated via Cloudflare |
| HTTPS cert | ⏳ Auto-provisioning (Let's Encrypt via GitHub) |
| "Enforce HTTPS" toggle | ⏳ Enable in repo Settings → Pages once cert is issued |
| Mascot illustration | ✅ Joe + Duke cartoon badge in hero |
| Pricing & services copy | ✅ |
| About section | ✅ |
| Booking embed | ✅ Cal.com popup embed wired to API-controlled event `jrwise100-jg1uaa/groom` |
| Gallery | ✅ Real gallery photos added |
| Cal.com account | ✅ Created for Joe; API key generated |
| Cal.com event/availability/intake config | ✅ API-controlled event inspected; exact confirmation email content still needs verification by test booking |

---

## Stack & architecture

- **Hosting:** GitHub Pages, free tier, serves from `main` branch root
- **Domain:** `dukesdoggrooming.com` (Cloudflare registrar, ~$10/year)
- **DNS:** Cloudflare, "DNS only" (gray cloud) records — 4 A records to GitHub Pages IPs + CNAME for `www`
- **Booking:** Cal.com (free tier), popup embed via `data-cal-link="jrwise100-jg1uaa/groom"`
- **Build step:** none — pure static HTML/CSS/JS
- **Total annual cost:** ~$10 (just the domain)

### DNS records at Cloudflare

```
A     @     185.199.108.153    DNS only
A     @     185.199.109.153    DNS only
A     @     185.199.110.153    DNS only
A     @     185.199.111.153    DNS only
CNAME www   countmiller-alt.github.io   DNS only
```

---

## File structure

```
.
├── index.html          # Single-page site, all sections
├── styles.css          # Global styles, CSS variables for theming
├── script.js           # Mobile nav toggle + footer year stamp
├── images/
│   └── joe-and-duke.png   # AI-generated mascot illustration (800x800, ~750KB)
├── CNAME               # Tells GitHub Pages the custom domain
├── .gitignore          # Blocks secrets and tooling artifacts
└── PROJECT_NOTES.md    # This file
```

---

## Brand & design

- **Name:** Duke's Dog Grooming (Duke = Joe's standard poodle, the mascot/namesake)
- **Tagline:** "Royal treatment. No royal pricing."
- **Color palette:**
  - Background: `#FAF7F2` (warm cream)
  - Tinted sections: `#F2EDE3`
  - Text: `#1F2937` (slate)
  - Accent: `#5B8A72` (sage)
  - Accent-dark: `#436A57`
- **Typography:** Fraunces (serif headers, Google Fonts) + Inter (body, Google Fonts)
- **Design vibe:** Clean and modern with light humor in copy ("Your hardwood floors will write you a thank-you note")
- **Visual hook:** Cartoon mascot badge of Joe in apron with Duke (poodle wearing tiny crown), sage-green circular background

---

## Privacy posture

Joe grooms out of his home, so the public site is deliberately scrubbed of personal contact info:

- **No home address on site.** Service area is listed as "Tulsa, OK" only. Real address is shared via Cal.com booking confirmation email after a customer books.
- **No phone on site.** All contact routes through online booking. Joe explicitly didn't want direct calls.
- **No email on site.** Same reasoning.

The `main` branch was created as an orphan branch (no history) so even the early commits where these details were briefly present don't appear in the public repo.

---

## Cal.com setup — pending tasks

Joe's Cal.com account is created. The API key is stored **outside** the repo in the sandbox at `~/.cal-api-key` (file perms `600`). It is also intended to be saved in the user's password manager. **Never commit this key to the repo** — `.gitignore` has defenses against it but they're not bulletproof.

**Configuration to apply** (either via API or Cal.com web UI):

| Setting | Value |
|---|---|
| Username | `jrwise100-jg1uaa` (URL is `cal.com/jrwise100-jg1uaa/groom`; older notes expected `dukes-dog-grooming`, but the provided live API key controls `jrwise100-jg1uaa`) |
| Timezone | `America/Chicago` (Central) |
| Availability | Mon, Tue, Thu, Fri — 9:00 AM to 1:00 PM |
| Event type title | `Dog Grooming Appointment` |
| Event slug | `groom` |
| Event duration | 90 minutes (yields 9:00 AM and 10:30 AM bookable slots) |
| Description | "Full groom for your pup - bath, cut and style, sanitary trim, and paw-pad trim. Small dogs $50, large dogs $80. Add a nail trim for $10." |
| Location | "Joe's home studio (address shared after booking)" |

**Intake questions to add:**

1. Dog's name (short text, required)
2. Breed (short text, required)
3. Age (short text, required)
4. Gender (multiple choice: Male/Female, required)
5. Size (multiple choice: Small — $50 / Large — $80, required)
6. Add a nail trim? (multiple choice: Yes / No, required)
7. Anything else Joe should know? (long text, optional)
8. Photo of your pup (file upload, optional — skip if not on free tier)

**Confirmation email** should include Joe's actual home address and parking instructions. Address is stored externally; do not put it in the repo. As of 2026-05-06, the API-controlled Cal.com event has an address location with `public: false`; Cal.com documents that hidden locations are shown after booking confirmation. The exact attendee email body still needs verification by a test booking.

The current site uses Cal.com's popup embed instead of the older inline calendar. Booking CTAs carry `data-cal-link="jrwise100-jg1uaa/groom"` and the Cal.com script is loaded near the bottom of `index.html`.

---

## Outstanding work

1. Verify `https://dukesdoggrooming.com` loads with valid cert
2. Enable **Enforce HTTPS** in repo Settings → Pages
3. Verify Cal.com attendee confirmation email by making a test booking or logging into Joe's Cal.com account
4. Confirm the attendee email includes Joe's address and parking instructions
5. Confirm the Cal.com event availability/intake questions still match the intended setup
6. (Optional) Set up free Cloudflare email forwarding so `joe@dukesdoggrooming.com` → Joe's actual email
7. (Optional) Further compress mascot image (currently ~750KB)
8. (Optional) Add `og:image` and other social-sharing meta tags
9. (Optional) Set up Playwright e2e tests for the site (Playwright is already available globally)

---

## Environmental gotchas (for future AI sessions)

The development sandbox firewalls outbound HTTP traffic to most hosts:

- ❌ `cal.com`, `app.cal.com`, `api.cal.com` — all return `403 host_not_allowed`
- ❌ `github.io` — same
- ❌ `google.com` — same
- ✅ `registry.npmjs.org` works (npm install OK)
- ✅ Git push/pull to GitHub works
- ✅ DNS queries via Node's `dns` module work
- ✅ Local Playwright works (chromium at `/opt/pw-browsers/chromium-1194`) — useful for screenshotting the local site during development

**Implication:** Anything that requires live network access to Cal.com (API calls, browser automation, signup) must be done from a normal-network environment (your laptop), not from this sandbox. Setup walkthroughs and code edits work fine here.

---

## Key decisions and rationale

| Decision | Why |
|---|---|
| GitHub Pages over a Digital Ocean droplet | Site is 100% static. No backend needed. Saves ~$72/year for zero functional difference. |
| Cloudflare for the registrar | Sells domains at-cost (no markup), free DNS, free email forwarding, clean UI. |
| `.com` over a free subdomain | Real businesses need a `.com`. Once Joe approved the concept, the $10/yr was worth it. |
| Apex domain as primary (`dukesdoggrooming.com` not `www.`) | Modern convention. Easier to type and share. www auto-redirects. |
| Single event type instead of separate small/large dog events | Cleaner booking flow. Customers can't pick wrong size. Pricing handled by intake question. |
| 2-hour event duration | Lets Cal.com auto-derive 9 AM and 11 AM as the two daily slots from a 9–1 availability window — exactly what Joe wanted, no custom code needed. |
| Cartoon mascot badge over real photo of Joe | Avoids putting a real photo of Joe (with his daughter visible in background) on a public site. Cartoon also doubles as a logo. |
| Real photo of Duke in gallery | Originally planned but not yet added — privacy footprint is low (just a poodle in a yard) but waiting on Joe's curated photo set. |
| Orphan `main` branch | Lets us make repo public without exposing earlier commits that briefly contained the home address and phone number. |

---

## Branch strategy

- **`main`** — production. What GitHub Pages serves. Push here for deploys. Single linear history.
- **`claude/init-project-WR4RT`** — original dev branch with full commit history. Mostly retired now; safe to ignore or delete. GitHub may still clone this branch by default, so switch to `main` before reviewing or editing the live site.

---

## Recovery / pickup instructions

To continue on a different machine:

```bash
git clone https://github.com/countmiller-alt/Poodle-Groomer.git
cd Poodle-Groomer
# Open index.html in a browser, or:
npx http-server -p 8000   # then visit http://localhost:8000
```

To screenshot the local site at multiple viewports (handy when iterating on layout):

```bash
PLAYWRIGHT_BROWSERS_PATH=/opt/pw-browsers npx playwright screenshot \
  --browser chromium --viewport-size=390,844 --full-page \
  http://localhost:8000/ mobile.png
```

The Cal.com API key is **not** in this repo. Pull it from your password manager.
