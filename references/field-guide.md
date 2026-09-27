# App Store Connect field guide

What to put in each field, in the order you meet them. Values come from the
Step 0 fact sheet. "Default" means the answer for the typical small app: no
accounts, no ads, data on the device, and an optional yearly subscription.
Any app that breaks that pattern needs its real answer instead.

Apple renames and moves things. If a field here doesn't match the page, trust
the page and update this file afterwards.

## New App (Apps → + → New App)

| Field | Value |
|---|---|
| Platforms | iOS only (add macOS/visionOS only if asked) |
| Name | App name, 30 chars max, globally unique. It can differ from the home-screen name |
| Primary Language | English (U.S.) |
| Bundle ID | From `app.json`. Not listed? See SKILL.md Step 3 |
| SKU | The Expo slug (e.g. `farkle-score-tracker`). Internal only, and can never be changed |
| User Access | Full Access |

## App Information (General → App Information)

| Field | Value |
|---|---|
| Subtitle | From the listing doc, 30 chars max |
| Category | Primary and optional secondary from the listing doc. If the doc doesn't say, ASK. Don't guess |
| Content Rights | Default: "does not contain third-party content". **Yes** if the app shows third-party material (movie posters, API data like OMDb). Then confirm the rights question honestly |
| Age Rating | Click Edit and answer the questionnaire (below) |
| License Agreement | Leave Apple's standard EULA. The paywall's Terms link usually points to it |

**Age rating questionnaire.** Apple's tiers are 4+, 9+, 13+, 16+, 18+. For a
score tracker, every content question is **None** or **No**. That covers
violence, sexual content, profanity, horror, drugs, gambling, contests,
unrestricted web access, user-generated content, messaging, and ads. Result:
4+. Answer the medical and wellness questions honestly for health apps like
the GLP-1 tracker. A medication or dose tracker is not medical treatment
advice unless it gives advice. Media apps with mature synopses or posters
(Watchlisted) answered 12+/13+ before. Match the listing doc.

**The questionnaire (Sep 2026)** is a 7-step wizard with unlabeled radio
buttons. Group them by `name` and read each group's row text. Yes/no groups use
values `false`/`true`, and frequency groups use `NONE`/`INFREQUENT`/`FREQUENT`.
Step 1 also asks about parental controls and age assurance (No, unless the app
has them). Step 7 shows the calculated rating. Leave "Age Categories and
Override" on Not Applicable and Save.

**Regulated medical device** (asked for health apps): **No** for trackers
and loggers like GLP-1 Anchor. They don't diagnose or treat anything. Keep
the in-app medical disclaimer.

## Pricing and Availability

- Price: **Free**. The subscription is priced separately.
- Availability: all countries or regions.
- Leave everything else at its default: pre-orders off, the Mac/visionOS
  "make available" boxes as Apple sets them.

## App Privacy

1. **Privacy Policy URL**: the live URL from the fact sheet
   (the app's config first, else `$ASC_PRIVACY_URL_TEMPLATE` with `{app}` filled in, then check it loads).
2. **Data collection**: default is **No, we do not collect data from this
   app** ("Data Not Collected"). Purchases through StoreKit don't count,
   because Apple processes them. Declare real collection for any analytics,
   crash reporting, auth, or backend SDK found in `package.json`.
3. **Publish**. It isn't live until published.

## Subscription (Monetization → Subscriptions)

1. **Subscription group**: Reference Name `<App> Premium`. Then add the group's
   **App Store Localization** (English U.S.): display name `<App> Premium`.
   A group without a localization leaves its products stuck in "Missing
   Metadata".
2. **Create subscription**:
   - Reference Name: `<App> Premium Yearly` (internal)
   - Product ID: **exactly** the ID from the code (`<app>_premium_yearly` by
     convention). It's permanent and can't be reused if deleted.
3. On the product page (Sep 2026 layout):
   - Availability has no "select all". Tick each region header ("Europe
     (0)" etc.) to select every country in it. Leave "automatically available
     in future countries" and the new "Monthly with a 12-Month Commitment"
     plan off unless the code sells them.
   - Subscription Duration: 1 Year (or whatever the code says)
   - Availability: all countries or regions
   - Subscription Prices: add the price from the code in **United States
     (USD)**, then accept Apple's automatic equalization for other countries
   - App Store Localization (English U.S.): Display Name (e.g. `Premium —
     Yearly`) and Description (≤ 55 chars, e.g. `Unlimited games and custom
     target scores`). Take wording from the paywall copy when it exists.
   - Review Information: a screenshot of the paywall **for each product
     separately** (monthly and yearly each need their own; a simulator
     capture of the paywall with that plan selected works; at least
     640×920), plus a review note on how to reach it (e.g. "Start a second
     game to hit the free limit").
   - Family Sharing: off unless the repo says otherwise
4. Status should read **Ready to Submit**. "Missing Metadata" means a field
   above is empty, most often the group localization or a review
   screenshot. A clock icon next to the price means pricing is still
   processing, not missing.

**Subscription group localization** (group page → Display Name → Create): Save
stays disabled until the **Localization** select has a language (`$B select
@ref en-US`). It then showed "An error has occurred. Try again later." but had
saved — reload before retrying, or you create duplicates.

## Version page (iOS App → 1.0 Prepare for Submission)

| Field | Value |
|---|---|
| Promotional Text | Listing doc, 170 chars max (editable later without review) |
| Description | Listing doc, 4000 chars max. For apps with subscriptions, end with the two required links: `Privacy Policy: <url>` and `Terms of Use (EULA): https://www.apple.com/legal/internet-services/itunes/dev/stdeula/` |
| Keywords | Listing doc, 100 chars max, commas and no spaces |
| Support URL | From the app's config, else `$ASC_SUPPORT_URL`. A `mailto:` isn't accepted |
| Marketing URL | Optional; leave blank if none |
| Copyright | `© <year> $ASC_COPYRIGHT_HOLDER` |
| iPhone screenshots | Upload into whichever iPhone slot the page shows. It took 6.5" (1284×2778) and rejected 1320×2868 there in Aug 2026, so use the matching size from the screenshots `iPhone all-sizes/` folder. One file per upload, in slide order |
| iPad screenshots | 13" set, 2064×2752, only when `supportsTablet` is true. iPhone-only apps (GLP-1) skip this |
| App Preview video | Skip unless provided |
| Build | Select once processed (SKILL.md Step 8) |
| Game Center | Off |

**App Review Information**
- Sign-in required: **No** (no accounts). If the app has accounts, the user
  provides a demo login and you type in only what they give you.
- Contact: first and last name, email (`$ASC_CONTACT_EMAIL`), phone number
  (`$ASC_CONTACT_PHONE`; if it's unset, **ask**, and never make one up).
- Notes: write them paste-ready. Say what the app does in one line, how to
  reach the paywall, the product IDs, "no account needed; data stays on
  device", and for subscriptions "purchase and restore can be tested with
  a Sandbox account".

**Filling the version page by script (Sep 2026).** Promotional text,
description, keywords and Version accept values set with the native value
setter plus `input`/`change` events. The **App Review Information** fields
(names, phone, email, Notes) don't: type them with `$B fill @ref`. The
**Sign-in required** checkbox ignores `$B click` on the input (times out), a
JS `.click()`, and Space: click its `<label>` instead (tag it, then
`$B click 'label[data-x="1"]'`). Reload and read every field back.

**Version number must match the binary.** App Store Connect creates the first
version as "1.0"; an Expo app's binary says `1.0.0`. Set the page's Version
field to the binary's `CFBundleShortVersionString` before attaching a build.

**Version Release**: "Automatically release this version" unless the user asks
for a manual release.

## Export compliance (on selecting the build)

When `ITSAppUsesNonExemptEncryption` is `false` in `app.json`/Info.plist,
Apple usually doesn't ask. If it does, answer: uses encryption → only
exempt/standard (HTTPS) → **None of the algorithms mentioned above**.
