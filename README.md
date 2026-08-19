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
  campaign registration**: sender + program description, personalized first-text
  preview bubble (doubles as sample message #1), frequency/rates/HELP/STOP, and the
  bolded no-sharing declaration. Decline is "No thanks — I'll view it online."
- `?terms=v1` restores the previous screens; `exclusive` / `maxsold` / `inline` /
  `inline-maxsold` ride on v1 (see `compare.html`). If the live
  `GetContactOptInNames` returns multiple pros under v2, the checkbox sentence joins
  the names (per-pro checkboxes render in the roster line; a dedicated v2 multi-pro
  treatment is future work).

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
