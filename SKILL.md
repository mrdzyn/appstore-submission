---
name: app-store-submission
description: Use for writing/reviewing App Store or Google Play listings (name, subtitle, description, keywords, ASO, ratings, privacy, checklist) or for auditing a pointed-to project folder's README/docs/manifests for submission compliance risks (permissions, SDKs, tracking, content flags).
---

# App Store & Google Play Submission Kit

This skill turns either a pile of facts about an app, or a pointer to its actual codebase, into copy-paste-ready store listing copy for Apple's App Store Connect and Google Play Console, plus the full non-copy checklist needed to actually submit. Apple and Google score, truncate, and index listings differently, so treat them as two separate writing tasks that happen to share source material, not one description reused twice.

## Step 0 — If given a project folder, audit it before asking questions

When the user points to a project folder instead of (or in addition to) typing details manually, read the codebase first and use it to answer as much of Step 1's intake as possible — only ask the user for what genuinely can't be inferred (target audience, tone, pricing, launch countries, whether this is a new submission or an update).

**Pull app facts from documentation:** README.md, CHANGELOG.md, any `docs/*.md`, CONTRIBUTING.md. These usually already contain the app name, a description of what it does, a feature list, and version history — don't make the user retype what's already written down.

**Pull structured facts from platform config, and treat what you find as the source of truth for what the app actually does** (docs go stale; manifests don't):

| Platform | Files to check | What to extract |
|---|---|---|
| iOS | `Info.plist` | Display name, version, and every `NSxxxUsageDescription` key — each one is both a permission the app requests and the exact justification text Apple expects to see reflected in the privacy answers |
| iOS | `*.entitlements` | Capabilities in use: push notifications, HealthKit, background modes, Sign in with Apple, etc. |
| iOS | `Podfile`, `Podfile.lock`, `Package.resolved` | Third-party SDKs — especially ad networks, analytics, crash reporting, and attribution libraries, since these collect data even when the developer's own code doesn't |
| Android | `AndroidManifest.xml` | Full `<uses-permission>` list and `targetSdkVersion` |
| Android | `build.gradle` / `build.gradle.kts` (app module) | Dependencies — same SDK-footprint check as iOS |
| Cross-platform | `app.json` / `app.config.js` (Expo), `pubspec.yaml` (Flutter), `package.json` | Declared permissions/plugins and dependency list |

**Turn what you found into a restrictions/flags list** before writing any copy, and hand it to the user as a short audit summary:

- Every sensitive permission requested (location — especially background — camera, microphone, contacts, health data, SMS, call log, Bluetooth). Each one needs both a usage-justification string in the app and a matching entry in Step 4's privacy disclosures.
- Any data-collecting third-party SDK (ads, analytics, crash reporting, attribution) found in dependencies — flag it even if the user didn't mention it, since an SDK detected in the build but missing from the privacy answers is a common rejection and post-release takedown cause.
- iOS: mismatch between App Tracking Transparency (ATT) usage and actual presence of IDFA/tracking SDKs — either direction is a red flag worth surfacing.
- Non-exempt encryption use beyond standard HTTPS, which affects Apple's export compliance question.
- Content-risk signals worth a second look: gambling mechanics, crypto/financial features, health claims, user-generated content or chat (review teams want to see a moderation plan), alcohol or mature content — these shift the age rating and sometimes require extra notes for the reviewer.

Cross-reference this list against Step 4's checklist so nothing the code actually does goes undeclared in the store's privacy/data-safety questionnaire.

Be upfront about the limits of this pass: it's a static read of the repo, not a runtime or network audit. It catches declared permissions and known SDK footprints, not runtime behavior — present findings as a strong starting checklist for the developer to confirm, not a compliance guarantee. This also isn't a general code-security review (secrets, vulnerabilities); flag anything alarming you happen to notice, but stay focused on store-submission risk.

## Step 1 — Intake (fill gaps not already answered by Step 0)

Gather (only ask for what's still missing after checking the folder, or if no folder was given, ask directly):

- App name (or working title) and a one-sentence pitch
- Target audience and the 3–5 features or benefits that matter most to them
- Platforms: iOS, Android, or both
- Category (e.g. Productivity, Health & Fitness, Games > Puzzle)
- New submission or updating an existing live listing (if updating, ask for the current listing text so you edit rather than guess)
- Pricing model: free, paid, subscription, in-app purchases
- Any words/phrases users already search for to find apps like this
- Tone/voice preference (playful, professional, technical, etc.)
- Support URL and privacy policy URL (required fields on both stores)
- What user data the app collects and whether it's linked to identity or used for tracking/advertising — cross-check this against Step 0's SDK/permission findings rather than taking the answer at face value
- Target countries/languages for launch

Write down the answers as you go; you'll reuse them across every field below rather than asking twice.

## Step 2 — Write the listing copy

Write Apple and Google copy as separate passes. Reusing one description for both wastes Apple's dedicated keyword field and ignores that Google's algorithm mines the full description text for keywords since it has no separate field for them.

### Apple App Store Connect

| Field | Limit | Notes |
|---|---|---|
| App Name | 30 chars | The one field weighted most heavily for search. Keep the brand name readable — don't stuff keywords into it unless they're genuinely part of the name. |
| Subtitle | 30 chars | Sits under the name in search results. Complement the name — don't repeat words already used there; repeated words are wasted characters. |
| Promotional Text | 170 chars | The only field editable without a new app review. Use it for timely info: a sale, a new feature, a seasonal hook. Not indexed for search. |
| Description | 4000 chars | Not indexed for search ranking, but it's what converts a visitor into a download. The first 2–3 lines are visible before "more" truncates it — put the strongest hook there. Structure: hook → key benefits → feature list → social proof if available → call to action. |
| Keywords | 100 chars total, comma-separated, no spaces | Indexed for search. Don't repeat words already in the name, subtitle, or category — that's duplicated indexing wasted. Skip plurals (Apple matches word stems). Competitor brand names are against guidelines and risk rejection. |
| What's New | 4000 chars | For version updates — summarize what changed in plain language; if a folder was audited, mine the CHANGELOG rather than writing from scratch. |

### Google Play Console

| Field | Limit | Notes |
|---|---|---|
| App name | 30 chars | Also indexed for search, unlike Apple's name field which is search-relevant mainly through exact match. |
| Short description | 80 chars | The single most important field for both search ranking and the snippet shown in search results — it has to work standalone since many users never expand to the full description. |
| Full description | 4000 chars | Google's algorithm scans the entire text for keyword relevance — there's no separate keyword field, so the target keywords need to appear naturally 2–4 times across the description without reading as stuffed. Short paragraphs and light formatting (occasional emoji, line breaks) read fine here, unlike Apple's plainer rendering. |

After drafting, read both back and check: does the first line stand alone if nothing else is read? Does it sound like a person wrote it, not a keyword list?

## Step 3 — ASO / keyword strategy

- Brainstorm keywords from how a real user would search, not just feature names — "track expenses" not just "expense tracker" if the audience phrases it that way.
- Look at what competitor apps use in their titles/subtitles for ideas on established terms in the category.
- Prioritize keywords by relevance to the app first — a perfectly-matched niche term usually converts better than a high-volume term the app barely fits.
- Front-load: put the most important keywords in the fields weighted most (name > subtitle/short description > Apple's keywords field / Google's early description text), since later placement matters less.
- Write for the human reader first and the algorithm second — a description that reads as a keyword list will hurt conversion even if it ranks.
- Be upfront with the user that keyword suggestions here are based on naming and category best practice, not live search-volume data — Claude has no access to Apple's or Google's real-time keyword popularity tools. Recommend they validate final keyword choices against App Store Connect's own search terms report, Google Play Console's search terms data, or a third-party ASO tool before finalizing, especially if ranking for a competitive term matters a lot to them.
- Plan to revisit keywords after launch based on actual impression/conversion data rather than treating the first submission as final.

## Step 4 — Submission checklist (everything besides the copy)

Walk through this list with the user; check off what's ready and flag what's missing. If Step 0's folder audit surfaced permissions or SDKs, make sure they're reflected in the privacy/data-safety line item below rather than treated as a separate concern. Note up front that exact pixel dimensions and questionnaire wording on both platforms change periodically — give current best-known guidance below, but tell the user to confirm the live specs in App Store Connect / Play Console at submission time (or check directly if browser tools are available), since submitting against stale dimensions is a common rejection cause.

- **App icon** — Apple: 1024×1024 PNG, no transparency, no pre-rounded corners (the store applies the mask). Google: 512×512 PNG, 32-bit with alpha.
- **Screenshots** — both stores require screenshots for each supported device size class (e.g. iPhone, iPad on Apple; phone, 7" and 10" tablet on Google, if supporting tablets). Confirm the current required device sizes and minimum counts (usually at least 2–3 per size class) in the console before finalizing artwork.
- **Preview video** (optional on both) — short, silent-friendly since many users browse muted; captions/on-screen text carry the message.
- **Category & subcategory** — pick the category real users would browse to find this app, not the most flattering one; miscategorized apps get buried or rejected.
- **Age / content rating** — Apple uses a questionnaire about violence, mature content, gambling, user-generated content, etc. Google's is the IARC questionnaire, similar territory. Answer honestly based on actual app content — under-rating to reach a wider audience is a common rejection and removal reason. Cross-check against any content-risk flags from Step 0.
- **Privacy disclosures** — Apple's "App Privacy" nutrition label and Google's "Data safety" section both ask: what data types are collected, whether each is linked to the user's identity, and whether it's used for tracking/advertising. Map this directly from Step 0's permission/SDK findings and Step 1's answer — don't default to "we don't collect data" if the app has any analytics, crash reporting, or ad SDKs.
- **Pricing & availability** — price tier (or free), which countries/territories to launch in, and for Apple, whether the app uses in-app purchases or subscriptions (each needs its own product entry).
- **Contact & legal info** — support URL (required), marketing URL (optional), copyright holder name, and for Apple, an export compliance declaration about encryption use.
- **Trademark/guideline check** — the app name and keywords shouldn't include competitor brand names or unapproved references to "App Store"/"Google Play" wording; both stores reject on this.
- **Claims check** — flag any health, financial, or "#1" superlative claims in the copy that would need a disclaimer or evidence, since these are common review-rejection triggers.

## Step 5 — Deliver as a filled-in template

Produce one document with both stores' fields filled in and the checklist as checkboxes, so the user can copy-paste straight into App Store Connect and Play Console. If a folder was audited, lead with the restrictions/flags summary from Step 0 so it doesn't get buried under the copy. Use this skeleton:

```
# [App Name] — Store Submission Package

## Compliance flags found in the codebase (if a folder was audited)
- Permission: ... → needs privacy disclosure entry
- SDK detected: ... → needs privacy disclosure entry
- Other flags: ...

## Apple App Store Connect
- App Name (30):
- Subtitle (30):
- Promotional Text (170):
- Description (4000):
- Keywords (100, comma-separated no spaces):
- What's New (4000):

## Google Play Console
- App name (30):
- Short description (80):
- Full description (4000):

## Submission checklist
- [ ] App icon (1024×1024 Apple / 512×512 Google)
- [ ] Screenshots for each required device size — verify current sizes in console
- [ ] Preview video (optional)
- [ ] Category / subcategory
- [ ] Age / content rating questionnaire completed honestly
- [ ] Privacy / Data safety disclosures mapped from actual data collection
- [ ] Pricing tier + launch territories set
- [ ] Support URL, marketing URL, copyright, export compliance (Apple)
- [ ] Trademark and claims check passed
```

If the user wants this as a saved file (they said "save", named a format, or will clearly reuse it across a release cycle), create it as a markdown or docx file rather than only pasting it in chat. If it's a one-off quick pass, inline in the conversation is fine.

Always run the character counts on the final copy before handing it over — going even one character over a limit means the field gets silently truncated by the console, which is worse than a shorter draft.
