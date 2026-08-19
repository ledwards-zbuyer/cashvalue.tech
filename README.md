# cashvalue.tech

Standalone demo of the zBuyer home-seller path (the "Cash Value" funnel), served by GitHub
Pages at **https://cashvalue.tech**. Mirror of the `ledwards-zbuyer/new-zbuyer` mockups
seller path @ `52087da`, rebranded **zBuyer dba CashValue.tech**.

## Pages

| Page | Source (`new-zbuyer/mockups/`) | Purpose |
|---|---|---|
| `index.html` | `option-1-classic.html` | Homepage front door: address-entry hero + full funnel modal (**live** Pulse API). Stripped of all agents/investors, buy-side, and zbuyer.com links — consumer seller path only. |
| `go.html` | `landing-classic-blue.html` | DR lander for paid traffic (`data-dr` modal lock-in, same live funnel) |
| `report.html` | `report-classic-blue.html` | Cash Value Report — the funnel destination |
| `intel.html` | `report-intel-classic-blue.html` | Property Intelligence Report ("Go deeper" from the report) |
| `terms.html` / `privacy.html` | `terms-` / `privacy-classic-blue.html` | Legal pages (template copy) |
| `compare.html` | `compare-terms.html` | Internal chooser: consent v2 vs v1 + v1 sale-model variants |
| `shots/harness.html` | `shots/harness.html` | QA step-driver (iframes the homepage) |

## Consent architecture v2 (the default)

Both funnel pages ship the rebuilt opt-in screens:

- **Contact step** — the member's consent is an **optional checkbox** naming only the
  matched agent ("independent licensed agent"); zBuyer appears in no mobile consent on
  this screen. Declining still delivers the report, skips the expert handoff (no
  RealtorOpt / opt-in contact saves, neutral all-set copy). The clickwrap names
  zBuyer dba CashValue.tech for Terms/Privacy.
- **SMS step** — the campaign's own 10DLC opt-in and **the screenshot to submit with
  campaign registration**: sender + program description, frequency/rates/HELP/STOP,
  and the bolded no-sharing declaration. Decline is "No thanks — I'll view it online."
- `?terms=v1` restores the previous screens; `exclusive` / `maxsold` / `inline` /
  `inline-maxsold` ride on v1 (see `compare.html`).
- **v2 is exclusive-only**: whatever the live `GetContactOptInNames` returns, only the
  **first** contact is shown in the consent sentence, and only that contact's ID is
  recorded on opt-in — no roster, no per-pro checkboxes. The v1 previews keep the
  multi-pro render for comparison.

## ⚠️ The lander is live

`index.html` posts to the real Pulse Path lead API on **every page load** (`InitNewLead`
fires on load; `FinalizeLead` on the all-set CTA). **Any URL opened for testing must carry
`?mid=56&affid=testing`** so leads are recorded as test leads. `compare.html` and the
harness already bake these params into their links.

## Param quick-reference

- `?terms=v1|exclusive|maxsold|inline|inline-maxsold` — consent-step variants (all
  restore the v1 screens; default is v2). Forcing a sale model skips the live
  `GetContactOptInNames` render; **leads still post under every variant**.
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
3. **Smarty**: done — `assets/smarty-config.js` carries the cashvalue.tech embedded
   key (host-locked; make sure its allow-list includes `cashvalue.tech`,
   `www.cashvalue.tech`, and `localhost` for local QA).
4. **Google Cloud**: done — `assets/google-config.js` carries a referrer-locked key
   (project "My First Project", restricted to Maps JavaScript API + Geocoding API + Street View Static API,
   referrers `cashvalue.tech/*`, `www.cashvalue.tech/*`, `localhost:8741/*`). Note:
   Maps serves only billing-enabled projects — activate the account before the
   free-trial credit lapses or this prepop fallback quietly stops.

## Caveats

- Consent/terms wording is **placeholder pending final legal language**
  (see new-zbuyer `HANDOFF.md` §5).
- `terms.html` / `privacy.html` carry template-notice banners — replace with finalized
  policies before real traffic.
- `robots.txt` disallows all crawling (demo posture); funnel pages are also `noindex`.
- Favicon is still the zBuyer Z-sphere mark — swap when a CashValue.tech mark exists.
