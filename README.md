# cashvalue.tech

Standalone demo of the zBuyer home-seller path (the "Cash Value" funnel), served by GitHub
Pages at **https://cashvalue.tech**. Mirror of the `ledwards-zbuyer/new-zbuyer` mockups
seller path @ `52087da`, rebranded **zBuyer dba CashValue.tech**.

## Pages

| Page | Source (`new-zbuyer/mockups/`) | Purpose |
|---|---|---|
| `index.html` | `landing-classic-blue.html` | DR lander + full lead-funnel modal (**live** Pulse API) |
| `report.html` | `report-classic-blue.html` | Cash Value Report — the funnel destination |
| `intel.html` | `report-intel-classic-blue.html` | Property Intelligence Report ("Go deeper" from the report) |
| `terms.html` / `privacy.html` | `terms-` / `privacy-classic-blue.html` | Legal pages (template copy) |
| `compare.html` | `compare-terms.html` | Internal chooser for consent/terms variants |
| `shots/harness.html` | `shots/harness.html` | QA step-driver (iframes the lander) |

## ⚠️ The lander is live

`index.html` posts to the real Pulse Path lead API on **every page load** (`InitNewLead`
fires on load; `FinalizeLead` on the all-set CTA). **Any URL opened for testing must carry
`?mid=56&affid=testing`** so leads are recorded as test leads. `compare.html` and the
harness already bake these params into their links.

## Param quick-reference

- `?terms=exclusive|maxsold|inline|inline-maxsold` — consent-step variants. Forcing a
  variant skips the live `GetContactOptInNames` render; **leads still post**.
- `?sms=0` — skip the "Access your report anytime" step (rail becomes 4 steps)
- `?dnc=0` — remove the "Do not contact me" link on the all-set step
- z-params (`zfname zlastname zphone zemail zstreet zcity zstate zzipcode`) — publisher prepop
- `report.html`: `?debug=1`, `?demoPhone=`, `?values=`, `?ranges=1`, `?snap=`
- `shots/harness.html`: `?step=contact|err|questions|spdrag|textreport|allset|dnc|ac`,
  `&terms=` shortcut, `?src=` page override

## Local QA

```
python -m http.server 8741
```

The port matters: the Google key's referrer allowlist includes `localhost:8741`, and
Smarty's host allowlist includes `localhost`. Open
`http://localhost:8741/index.html?mid=56&affid=testing` — never omit the test params.

## Deploy / domain setup (manual, one-time)

1. **Repo Settings → Pages**: Deploy from branch `main` / `(root)`; custom domain
   `cashvalue.tech`; tick Enforce HTTPS once the certificate issues.
2. **DNS**: apex `A` → `185.199.108.153`, `185.199.109.153`, `185.199.110.153`,
   `185.199.111.153`; `www` `CNAME` → `ledwards-zbuyer.github.io`.
3. **Smarty** (smarty.com/account/keys, embedded key): add hosts `cashvalue.tech` and
   `www.cashvalue.tech` — without this, address autocomplete fails on the live domain.
4. **Google Cloud**: add HTTP referrers `https://cashvalue.tech/*` and
   `https://www.cashvalue.tech/*` to the Maps/Geocoding key in `assets/google-config.js`.

## Caveats

- Consent/terms wording is **placeholder pending final legal language**
  (see new-zbuyer `HANDOFF.md` §5).
- `terms.html` / `privacy.html` carry template-notice banners — replace with finalized
  policies before real traffic.
- `robots.txt` disallows all crawling (demo posture); funnel pages are also `noindex`.
- Favicon is still the zBuyer Z-sphere mark — swap when a CashValue.tech mark exists.
