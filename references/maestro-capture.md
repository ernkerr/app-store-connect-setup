# Capturing screenshots with Maestro

Raw App Store captures, taken without the user tapping anything. The output
goes to the `app-store-screenshots` skill for styling. Maestro taps the real
app in the simulator by accessibility label, so the images are the actual
native UI. The flow is saved in the app repo (`.maestro/screenshots.yaml` plus
a `README.md`), so it can be re-run after any design change.

## Setup (once per machine)

```bash
brew install openjdk                                  # Maestro needs Java
curl -fsSL https://get.maestro.mobile.dev | bash
export PATH="$HOME/.maestro/bin:/opt/homebrew/opt/openjdk/bin:$PATH"
maestro --version
```

## Decide what to show (read the repo first)

1. Read the app's README, listing doc, and screens (`app/**`), plus the rules
   of the game or domain, so the demo data is realistic and exercises the
   selling features.
2. Pick 5 shots. The order that worked: the core job with real data (hero),
   the differentiating feature mid-use, depth (stats/history), an entry screen
   mid-use, then setup or personalization. Add the **paywall** as a 6th
   capture, since it doubles as the subscription's App Review screenshot.
3. Write out the demo data and check the arithmetic against the app's own
   rules before running (Farkle: an entry threshold meant a 150 turn didn't
   count).

## Learn the labels, don't guess them

```bash
maestro --device $UDID hierarchy > h.json    # then print text/accessibilityText per node
```
- RN Pressables merge their children into one label (e.g. `"M, Maya, 0"`,
  `"Maya, Jordan, Sam, Continue Game, Just now"`). Match with regex:
  `".*Continue Game.*"`, `"J, Jordan.*"`.
- Buttons with an `accessibilityLabel` are the reliable targets (`"Add a 1,
  worth 100 points"`, `"Farkle, bust this turn"`). If the app lacks them,
  adding labels is a good accessibility change anyway.
- Text inputs may all read `"Input Field"`. Address them with `index`, and
  `eraseText` before `inputText` because fields can be prefilled ("You").
- Labels change with state: "Show stats" becomes "Hide stats", and a
  repeated tap on "Face 2" becomes "Three 2s".
- The same control repeated in a list needs `index` (the second hot-dice
  calculator is `index: 1`).

## Flow shape

```yaml
appId: <bundle id>
---
- launchApp:
    clearState: true             # deterministic start; wipes that simulator's app data
# Dev builds only: Expo launcher, then the dev-menu intro. Release builds skip these.
- runFlow:
    when: { visible: "http://localhost:8081" }
    commands: [ { tapOn: "http://localhost:8081" } ]
- extendedWaitUntil: { visible: "(<first screen text>|This is the developer menu.*)", timeout: 60000 }
- runFlow:
    when: { visible: "This is the developer menu.*" }
    commands: [ { tapOn: "Continue" } ]
- runFlow:
    when: { visible: "Toggle performance monitor" }
    commands: [ { tapOn: "Close" } ]
# ... drive the app, then:
- takeScreenshot: 01-hero          # relative name: saved under --test-output-dir
```

Run once per device, with a clean status bar:

```bash
xcrun simctl status_bar $UDID override --time 9:41 --batteryState charged --batteryLevel 100 --wifiBars 3
maestro --device $UDID test .maestro/screenshots.yaml --test-output-dir <scratch>/shots-<device>
```

iPhone 17 Pro Max captures at 1320×2868 and iPad Pro 13" at 2064×2752,
already the App Store sizes.

## Gotchas

- `takeScreenshot` rejects absolute paths outside its output folder. Use
  relative names plus `--test-output-dir`.
- Prefer a **Release** simulator build (`npx expo run:ios --configuration
  Release --device $UDID`). Dev builds add the launcher, the dev-menu popup,
  and possible LogBox banners that could leak into a shot.
- `scrollUntilVisible` stops as soon as the target shows, often too far.
  Nudge back with a `swipe` (start 50%,35% → end 50%,55%) so related rows
  share the frame.
- A crash report from `XCTAutomationSupport` is Maestro's injected test
  runner crashing, not the app. The app can vanish right after a flow ends.
  Take screenshots inside the flow, not afterwards.
- Check every capture by eye (a contact sheet is quickest). Names, colors,
  totals and "nothing overlapping" are what go wrong.
