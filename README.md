# App Store & Google Play Submission Kit

A [Claude Skill](https://www.anthropic.com/news/skills) that turns app details — or a pointer to an app's actual codebase — into copy-paste-ready App Store Connect and Google Play Console listing copy, plus the full non-copy submission checklist.

## What it does

- **Writes listing copy for both stores separately** — Apple App Store Connect (name, subtitle, promotional text, description, keywords, what's new) and Google Play Console (name, short description, full description) — respecting each store's character limits and how each one actually indexes for search.
- **Audits a project folder**, when you point it at one: reads `README.md` / `CHANGELOG.md` / `docs/*.md` for app facts, and platform manifests (`Info.plist`, `AndroidManifest.xml`, `Podfile`, `build.gradle`, `app.json`, `pubspec.yaml`, `package.json`, etc.) for permissions and third-party SDKs — then flags anything that needs a matching privacy/data-safety disclosure, an ATT mismatch, an export-compliance trigger, or a content-rating concern.
- **Gives ASO / keyword strategy** — how to pick and prioritize keywords for each store, with an honest note that suggestions are best-practice reasoning, not live search-volume data.
- **Generates correctly-sized screenshot placeholders** — a real `assets/screenshots/` folder with a blank, exact-pixel-dimension PNG for every screenshot slot your platforms need, plus a `CHECKLIST.md` to tick off. No dependencies (pure Python stdlib), so it works even in a locked-down shell with no internet access.
- **Verifies your screenshots when you're done** — re-scans the folder, tells you which slots are still placeholders, which are the wrong size, and which are good to go, and updates the checklist itself.
- **Runs the full submission checklist** — icons, screenshots, age/content rating, privacy disclosures, pricing & availability, trademark and claims checks — with a reminder to verify exact current specs in the live consoles, since Apple and Google change these periodically.
- **Delivers a fill-in-the-blank template** ready to paste straight into both consoles.

## Installing

This is a [Claude Skill](https://www.anthropic.com/news/skills) — a markdown file with instructions Claude reads when a relevant task comes up. To use it:

1. Download [`SKILL.md`](./SKILL.md) from this repo.
2. Add it to your Claude Skills (in Claude.ai / Claude Code / Cowork, whichever surface you use) as a new skill named `app-store-submission`.
3. It triggers automatically on prompts like "write my app store listing," "play store description," "ASO," "app submission," or "audit this project for app store compliance" — or point it at your project folder directly.

## Usage examples

- *"Write my App Store and Play Store listing for a habit tracker called Loop — it's free with a premium subscription."*
- *"Audit ~/projects/my-app for anything that could hold up app store submission."*
- *"Here's my current Play Store description, can you tighten it up and suggest better keywords?"*
- *"Set up my screenshot folders and checklist for iOS and Android."* / *"I've added my screenshots, can you check them?"*

## Scope and limits

The codebase audit (Step 0) is a **static read** of the repo — it reads declared permissions and dependency manifests, not runtime behavior. Treat its output as a strong starting checklist to confirm, not a compliance guarantee. It's also not a general security review; it stays focused on what affects app store submission specifically.

Character limits, screenshot dimensions, and rating-questionnaire wording reflect Apple's and Google's published guidelines at the time this skill was written and can change — always confirm the current specs in App Store Connect / Play Console before submitting.

## License

MIT — see [LICENSE](./LICENSE). Use it, fork it, adapt it, no attribution required (though credit is appreciated).

## Contributing

Issues and PRs welcome — especially updates to character limits or checklist items if the stores change their requirements.
