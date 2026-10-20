# UTM Links – Usage Guide for embersurrogacy.com

This doc is the detailed implementation guide for UTM tracking on the Ember Surrogacy site. Every page has its own URL: `/`, `/intended-parents/`, `/become-a-surrogate/`, `/surrogate-application/`, `/contact/`, `/about/` (full list in `routes.json`). Old `#/page` links still work: they redirect to the new URL and keep their UTMs.

## How Tracking Works (code: `index.html:1239-1377`)

- **Parsing:** UTMs are parsed from `?utm_...`. Old `#/page?utm_...` links are converted to `/page/?utm_...` before anything else runs.
- **Keys tracked (`index.html:1241`):** `utm_source`, `utm_medium`, `utm_campaign`, `utm_term`, `utm_content`, `utm_id`, plus auto-click IDs `gclid` (Google), `fbclid`/`fbadid` (Meta), `msclkid` (Bing), `gbraid`/`wbraid`.
- **Persistence:** Stored as `ember_utms_v1` in both `localStorage` + `sessionStorage`, survives in-site navigation and Back/Forward (`index.html:1365-1366`).
- **Form injection:** Injected as hidden inputs (`data-utm`) into `contactForm` (Intended Parents) + `quizForm` (Surrogate quiz) (`index.html:1340-1356`).
- **Email:** Formatted into `MARKETING ATTRIBUTION` block in StaticForms email (`index.html:1300-1338`).
- **GA4:** Sent with every SPA `page_view` via `gtag('event', 'page_view', { ...utms })` (`index.html:1404-1409`).
- **First-touch context:** Also captures `_first_landing`, `_first_referrer`, `_first_seen` on first visit (`index.html:1285-1288`).

## Link Format

```
https://embersurrogacy.com/<page-path>/?utm_source=<source>&utm_medium=<medium>&utm_campaign=<campaign>&utm_content=<variant>
```

**Naming Rules:** lowercase, `_` or `+` separated, no spaces. Keep `utm_campaign` specific enough to split Parents vs. Surrogates funnels — it flows straight into the StaticForms email `info@embersurrogacy.com` receives.

### Quick-Build Cheat Sheet

| Param | Use for Ember | Example values |
|-------|---------------|----------------|
| `utm_source` | Platform / publisher | `instagram`, `facebook`, `google`, `linkedin`, `newsletter`, `podcast`, `referral_partner`, `ccrm` |
| `utm_medium` | Placement type | `social` (feed), `social_stories`, `social_reel`, `bio`, `cpc`, `organic`, `email`, `podcast`, `referral` |
| `utm_campaign` | Audience + quarter/goal | `parents_q3_2026`, `surrogates_q3_2026`, `brand_launch`, `ivf_clinic_cohort` |
| `utm_content` | A/B variant / creator / ad / asset | `header_cta`, `video_v2`, `carousel_slide2`, `dr_smith_reel`, `link_in_bio`, `qr_lobby_card` |
| `utm_term` | Paid keyword (optional) | `surrogacy+agency`, `become+a+surrogate`, `gestational+surrogacy+cost` |
| `utm_id` | GA4 campaign ID (optional) | Matches Google Ads / GA4 campaign import |

---

## 1. Instagram
Use `medium` to distinguish surface; `content` to tag post/reel/story variant so you know what creative drove the lead to `/surrogate-application/` vs `/intended-parents/`.

```
# Feed post -> Intended Parents page
https://embersurrogacy.com/intended-parents/?utm_source=instagram&utm_medium=social&utm_campaign=parents_q3_2026&utm_content=feed_carousel_v1

# Reel from surrogate ambassador -> Surrogate quiz (high-intent)
https://embersurrogacy.com/surrogate-application/?utm_source=instagram&utm_medium=social_reel&utm_campaign=surrogates_q3_2026&utm_content=ambassador_jess_reel2

# Stories swipe-up / link sticker -> Homepage with brand campaign
https://embersurrogacy.com/?utm_source=instagram&utm_medium=social_stories&utm_campaign=brand_awareness_q3&utm_content=story_poll_v2

# Link in bio (Linktree/Beacons) - generic fallback
https://embersurrogacy.com/?utm_source=instagram&utm_medium=bio&utm_campaign=always_on&utm_content=link_in_bio
```

**Use case:** Compare which format converts better to surrogate applications vs. parent inquiries.

## 2. Facebook (Meta)
Same as IG but keep `source=facebook` so GA4 doesn't bundle them. `fbclid` is auto-captured if an ad click appends it.

```
# FB Page organic post -> Contact
https://embersurrogacy.com/contact/?utm_source=facebook&utm_medium=social&utm_campaign=parents_q3_2026&utm_content=page_post_july18

# Paid Advantage+ campaign -> Parents (use utm_id for GA4 ↔ Ads linking)
https://embersurrogacy.com/intended-parents/?utm_source=facebook&utm_medium=cpc&utm_campaign=parents_search_2026&utm_content=adv_carousel_a&utm_id=12021345678

# FB Group share via community mod (track partner)
https://embersurrogacy.com/become-a-surrogate/?utm_source=facebook&utm_medium=social&utm_campaign=surrogates_q3_2026&utm_content=group_surro_squad_mod
```

**Use case:** Separate organic community building from paid acquisition for ROAS reporting.

## 3. Google
`medium=cpc` for Ads, `organic` for SEO content, auto `gclid` capture for attribution. Use `utm_term` only on paid search.

```
# Google Ads - Intended Parents search - Cost page keyword
https://embersurrogacy.com/intended-parents/?utm_source=google&utm_medium=cpc&utm_campaign=parents_q3_search&utm_term=surrogacy+agency+cost&utm_content=rsa_headline_a

# Google Ads - Surrogate acquisition
https://embersurrogacy.com/surrogate-application/?utm_source=google&utm_medium=cpc&utm_campaign=surrogates_q3_search&utm_term=become+a+surrogate&utm_content=rsa_headline_pay_comp

# Organic blog/press link back to site (you publish a guest post)
https://embersurrogacy.com/about/?utm_source=google&utm_medium=organic&utm_campaign=seo_guest_post&utm_content=parents_com_article

# Google Business Profile website button
https://embersurrogacy.com/?utm_source=google&utm_medium=organic&utm_campaign=gbp_website_btn&utm_content=profile_mainlink
```

**Use case:** Measure true paid keyword performance for two different personas (parents vs surrogates) — they have very different CPCs and LTVs.

## 4. LinkedIn
Primary B2B — referral partners (IVF clinics, attorneys), founder thought-leadership. Keep `source=linkedin`.

```
# Founder personal post -> Parents landing
https://embersurrogacy.com/intended-parents/?utm_source=linkedin&utm_medium=social&utm_campaign=parents_q3_2026&utm_content=founder_post_july_era_v2

# Company page - Surrogates hiring angle
https://embersurrogacy.com/become-a-surrogate/?utm_source=linkedin&utm_medium=social&utm_campaign=surrogates_q3_2026&utm_content=company_page_mission_video

# LinkedIn Ads - IVF Clinic referral partner campaign (high value)
https://embersurrogacy.com/contact/?utm_source=linkedin&utm_medium=cpc&utm_campaign=referral_ivf_q3_2026&utm_content=sponsored_inmail_clinic_v1&utm_id=linkedin_clinic_01

# Direct outreach to attorney partner network
https://embersurrogacy.com/?utm_source=linkedin&utm_medium=referral&utm_campaign=attorney_partners_q3&utm_content=dm_jody_template
```

**Use case:** Track which referral partner channel converts to qualified parent leads. LinkedIn outreach is high-intent for Ember's B2B2C model.

## 5. Newsletter / Email

```
https://embersurrogacy.com/intended-parents/?utm_source=newsletter&utm_medium=email&utm_campaign=parents_nurture_q3&utm_content=weekly_jul21_cta
https://embersurrogacy.com/surrogate-application/?utm_source=newsletter&utm_medium=email&utm_campaign=surrogates_drip_02&utm_content=comp_breakdown_btn
```

**Use case:** Drip attribution — know which email in a nurture sequence drove the second-touch application.

## 6. Podcast / PR / Speaking

```
https://embersurrogacy.com/?utm_source=podcast&utm_medium=podcast&utm_campaign=brand_q3&utm_content=podcast_infertility_untold_2026_07
```

**Use case:** Vanity URL alternative — give podcast hosts a trackable link that doesn't look like a shortener and still surfaces in GA4.

## 7. Referral Partner (IVF clinics, attorneys)

```
https://embersurrogacy.com/contact/?utm_source=ccrm&utm_medium=referral&utm_campaign=ivf_referral_q3_2026&utm_content=qr_lobby_card
https://embersurrogacy.com/surrogate-application/?utm_source=referral_partner&utm_medium=referral&utm_campaign=surrogates_local_q3&utm_content=clinic_xyz_flyer
```

**Use case:** Physical / offline attribution. One `utm_source` per clinic (`ccrm`, `shady_grove`, `rma`) lets you calculate referral value per clinic partnership.

## 8. A/B Testing CTAs (on-site or across assets)
This architecture stores `_first_landing` + `_first_referrer` + `_first_seen` (index.html:1285-1288), so if a user lands via `?utm_source=instagram` on `/` and later navigates to `/surrogate-application/` and submits, attribution still shows Original Landing = Instagram. Use `utm_content` to differentiate which button drove it:

```
?utm_source=instagram&utm_medium=social&utm_campaign=parents_q3_2026&utm_content=home_hero_cta
vs
?utm_source=instagram&utm_medium=social&utm_campaign=parents_q3_2026&utm_content=footer_cta
```

**Use case:** Creative testing — which CTA placement / video variant drives form submits, even with multi-page SPA navigation.

## 9. Verification

Add debug flag and check email + GA4:

```
https://embersurrogacy.com/?utm_source=linkedin&utm_medium=social&utm_campaign=test_q3&debug=1
```

Then navigate to `/contact/` or `/surrogate-application/` and submit a test. Check StaticForms email for `MARKETING ATTRIBUTION` block and GA4 → Reports → Traffic Acquisition for session source.

## 10. QR Codes
Reuse same UTM strings above — just encode the full URL into the QR via any generator. Use unique `utm_content=qr_lobby_card` vs `qr_event_banner` to tell apart scans. Example: clinic lobby card vs. conference booth banner both `source=ccrm` but different `content`.
