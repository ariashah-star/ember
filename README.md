# Ember Surrogacy Website 
[embersurrogacy.com](https://embersurrogacy.com/)

# Testing + Health Monitoring
### E2E Testing
1. Test that the form submission results in StaticForms submission
2. Test that the UTM parameters are included in the form submission email
3. Test that the Meta Pixel fires on form submission


### Monitoring
1. View Google Analytics dashboards for traffic
2. Check that Static Forms is not categorizing things into Spam

# UTM Parameters for Tracking Lead Source
UTM parameters are tracked across navigation (hash router `#/home`, `#/contact`, `#/apply`) and included in Staticforms email submissions for lead attribution, and also for analytics using GA4.

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

### UTM Parameter Inclusion in Form Submission Email
The UTM parameters are included in the form submissions (both parent form and surrogate form). This allows us to know where the lead came from in the form submission email that info@embersurrogacy.com receives from StaticForms.

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
https://embersurrogacy.com/#/parents?utm_source=instagram&utm_medium=social&utm_campaign=parents_q3_2026&utm_content=feed_carousel_v1

# LinkedIn founder post
https://embersurrogacy.com/#/parents?utm_source=linkedin&utm_medium=social&utm_campaign=parents_q3_2026&utm_content=founder_post_july_era_v2

# Google Ads -> Surrogate quiz
https://embersurrogacy.com/#/apply?utm_source=google&utm_medium=cpc&utm_campaign=surrogates_q3_search&utm_term=become+a+surrogate&utm_content=rsa_headline_pay_comp

# IVF clinic QR card
https://embersurrogacy.com/#/contact?utm_source=ccrm&utm_medium=referral&utm_campaign=ivf_referral_q3_2026&utm_content=qr_lobby_card
```
