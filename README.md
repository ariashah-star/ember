# Ember Surrogacy Website 
[embersurrogacy.com](https://embersurrogacy.com/)

# Page URLs and SEO
`index.html` is the only page to edit. Each page has its own URL, title and description, listed in `routes.json` (for example `/intended-parents/`, `/become-a-surrogate/`, `/surrogate-application/`).

After changing `index.html` or `routes.json`, run:
```
python3 scripts/build_pages.py
```
This regenerates each page's copy (e.g. `become-a-surrogate/index.html`), `404.html`, `sitemap.xml` and `robots.txt`, with the right title, canonical link, social cards and FAQ structured data. Commit the generated files too. A GitHub check fails on any pull request where they are out of date.

Old `#/page` links (ads, QR codes, bookmarks) still work: they redirect to the new URL and keep their UTM parameters.

To preview locally: `python3 -m http.server 3000`, then open http://localhost:3000/.

# Testing + Health Monitoring
# Lead intake
Both forms post to the **Ember Leads** Google Apps Script web app (`EMBER_LEADS_URL` in `index.html`), which runs in the info@embersurrogacy.com account. It files each lead into the **Surrogate Leads** or **IP Leads** Google Sheet, emails a new-lead alert, sends the applicant's instant reply and later follow-ups, and filters spam. The thank-you screen only shows once the script confirms the lead was saved. The script's source and setup steps live outside this repo, in the project files (`crm/Code.gs`, `crm/SETUP.md`); after changing it, publish a new version under Deploy → Manage deployments so the URL stays the same.

### E2E Testing
1. Submit each form and check the lead lands in its sheet, with UTM columns filled when the link had UTMs
2. Check the new-lead alert reaches info@ and the instant reply reaches the applicant (or the test address while the sheet's Settings tab has Test mode On)


### Monitoring
1. View Google Analytics dashboards for traffic
2. Check the Ember Leads script's Executions page for failed runs

# UTM Parameters for Tracking Lead Source
UTM parameters are tracked across page navigation and saved with each lead in the lead sheets for attribution, and also for analytics using GA4.

### UTM Parameter List
This is the complete list of UTM parameters that are tracked:
- `utm_source` - where traffic came from: `linkedin`, `google`, `newsletter`, `instagram`
- `utm_medium` - how: `social`, `cpc`, `email`, `organic`
- `utm_campaign` - which campaign: `test_q3`, `parents_q3`, `surrogates_promo`
- `utm_term` - paid search keyword (optional): `surrogacy+agency`
- `utm_content` - A/B variant (optional): `header_cta`, `video_v2`
- `utm_id` - GA4 campaign ID (optional)

### GA4 (Google Analytics)
In the info@embersurrogacy.com gmail account.

### Cookie consent
Nothing from Google or any other third party loads until the visitor clicks **Accept** on the cookie banner. Before that, `gtag()` calls are dropped and `gtag.js` is never requested. The choice is saved in the browser (`ember_cookie_consent` in localStorage), and the **Cookie Settings** link in the footer reopens the banner. Declining after accepting turns GA4 off and deletes its cookies. Browsers that send Global Privacy Control count as declined and don't see the banner. The quiz and health data pages never run analytics and don't show the banner.

Any new tracker (Meta Pixel, ad tags, chat widgets) must load only from `emberLoadAnalytics` in `index.html`, never with a plain `<script src>` tag.

The one exception is Cloudflare Turnstile, the spam check on the two forms. It is a strictly necessary security tool, not a tracker, so it loads without consent, and only once a visitor starts a form. It is off while `TURNSTILE_SITE_KEY` in `index.html` is empty.

Fonts are self-hosted in `fonts/` (SIL Open Font License), so pages make no requests to Google Fonts.

### UTM Parameter Inclusion in Form Submission Email
The UTM parameters are included in the form submissions (both parent form and surrogate form). Each lead's row in its sheet has UTM source, medium, campaign, content and term columns, plus the click ID, landing page and referrer. (Before the move to the lead sheets, StaticForms put them in the notification email as below.)

This is a sample of the marketing attribution section of the email:
```
...
----------------------------------------
MARKETING ATTRIBUTION
----------------------------------------
Source: linkedin
Medium: social
Campaign: test_q3
Landing Page: http://localhost:3000/?utm_source=linkedin&utm_medium=social&utm_campaign=test_q3
Referrer: (direct)
First Seen: 2026-07-21T15:13:37.062Z

Raw: utm_source=linkedin&utm_medium=social&utm_campaign=test_q3
```

### UTM Based Links

Full detailed guide with copy-paste links for every channel (Instagram, Facebook, Google, LinkedIn, Newsletter, Podcast, Referral Partners, QR Codes, A/B testing):

👉 **[See UTM_GUIDE.md](./UTM_GUIDE.md)**

**Quick examples:**

```
# Instagram feed -> Parents
https://embersurrogacy.com/intended-parents/?utm_source=instagram&utm_medium=social&utm_campaign=parents_q3_2026&utm_content=feed_carousel_v1

# LinkedIn founder post
https://embersurrogacy.com/intended-parents/?utm_source=linkedin&utm_medium=social&utm_campaign=parents_q3_2026&utm_content=founder_post_july_era_v2

# Google Ads -> Surrogate quiz
https://embersurrogacy.com/surrogate-application/?utm_source=google&utm_medium=cpc&utm_campaign=surrogates_q3_search&utm_term=become+a+surrogate&utm_content=rsa_headline_pay_comp

# IVF clinic QR card
https://embersurrogacy.com/contact/?utm_source=ccrm&utm_medium=referral&utm_campaign=ivf_referral_q3_2026&utm_content=qr_lobby_card
```
