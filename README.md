# app-store-connect-setup

A [Claude Code](https://claude.com/claude-code) **skill** that creates an iOS app
in App Store Connect and fills in every field Apple needs before review. That
covers the app record, App Information, age rating, pricing, App Privacy, the
auto-renewable subscription, the version page and screenshots, and the build.

It drives App Store Connect in gstack's browser. You sign in yourself, and it
never types passwords or 2FA codes. Every value comes from the app's own repo,
and it asks before it clicks **Submit for Review**.

It learns from each run: after every submission and every App Review verdict,
the skill writes what went wrong or changed back into itself. Fork it so
your lessons have somewhere to be pushed. See `SKILL.md` → "Learned the hard
way". Pull requests with generic lessons are welcome.

Companion to [app-store-screenshots](https://github.com/ernkerr/app-store-screenshots),
which makes the store images this skill uploads.

## Install

```bash
git clone https://github.com/ernkerr/app-store-connect-setup \
  ~/.claude/skills/app-store-connect-setup
```

Requires [gstack](https://github.com/garrytan/gstack) (for the `$B` browser).

## Configure

Set your developer details once, in `~/.claude/settings.json`:

```json
{
  "env": {
    "ASC_TEAM_ID": "ABCDE12345",
    "ASC_BUNDLE_PREFIX": "com.yourcompany",
    "ASC_COPYRIGHT_HOLDER": "Jane Doe",
    "ASC_CONTACT_EMAIL": "you@example.com",
    "ASC_SUPPORT_URL": "https://example.com",
    "ASC_PRIVACY_URL_TEMPLATE": "https://you.github.io/legal/{app}/"
  }
}
```

`ASC_CONTACT_PHONE` (the phone number for App Review) and
`ASC_PRIVACY_SITE_REPO` (the GitHub repo your privacy policies are published
from, e.g. `you/you.github.io`) are optional. If it's
unset, the skill asks each time. Any other value that's missing gets asked
for too, never guessed. See `SKILL.md` → Configuration.

## Use

From the app's repo, ask Claude Code:

> create this app in App Store Connect

## Files

- `SKILL.md`: the workflow, plus the running list of lessons.
- `references/field-guide.md`: what to put in each App Store Connect field.
