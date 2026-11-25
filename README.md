# Propofol Target Simulator

**Propofol Target Simulator** is a free, single-page web tool that suggests manual propofol infusion rates for a chosen target effect-site concentration. Pick a mock patient and a pharmacokinetic model, set a target, and it shows the rate to run now, when the next change is due, and how the rate steps down over the coming hours.

> **Open the app:** [gavinovsak.github.io/PropofolModels](https://gavinovsak.github.io/PropofolModels/)

No download, no login, no installation. Works on a phone, tablet or computer, and can be installed like an app that keeps working without a connection (see below).

> **For educational use only, not for clinical use.** This is not a medical device. It does not replace clinical judgment, your pump, or your institution's protocols. It uses a mock patient and has not been validated against any pump or against patient outcomes.

Part of the [#FOAM Apps directory](https://gavinovsak.github.io/FOAMapps/) (Free Open Access Medical education). The ribbon in the top right corner of the app links back to it.

---

## Who is this for?

- Anesthesia trainees learning how target-controlled infusion (TCI) rates behave over time
- Teachers who want to show why a manual infusion has to step down as drug accumulates
- Anyone curious how Schnider, Marsh and Eleveld differ for the same patient

---

## What it does

### Suggests one number to run now

The large figure is the suggested infusion rate in **mcg/kg/min**, rounded to the nearest 10. Beneath it you see the estimated effect-site concentration, a countdown to the next rate change, and the next few steps with clock times.

### Lets you change the target as the case goes on

Use the plus and minus buttons (0.5 mcg/mL steps) or type a target directly. Each change is time-stamped and carried forward.

### Shows the whole plan on a chart

A stepped chart plots the suggested rate over time, with earlier steps in grey and future steps at the current target. Hover or tap to read the rate and target at any time.

### Handles manual boluses

Log a bolus with a dose and a time (tap the chart to pick a time, or tap a logged bolus to edit it). Each bolus lowers the suggested rate for a few minutes, and a large one can pause it, so the effect site comes back to target.

### Matches a rate you set yourself

Tap the big number and enter the rate you actually set. The app tells you which target effect that rate corresponds to, and can adopt it as the new target.

### Optional BIS feedback (Eleveld only)

With the Eleveld model the app shows an expected BIS, prompts for a BIS value after a rate drop, keeps a log comparing predicted and entered values, and suggests a target nudge if BIS falls outside 40 to 60.

### Light and dark mode

The app follows your device setting by default. The sun/moon button next to the title switches it, and your choice is remembered in the browser.

### Installs as an app and works offline

It is a progressive web app (PWA). After the first visit it loads without a connection.

- **iPhone/iPad:** open the link in Safari, tap the Share icon, then *Add to Home Screen*
- **Android:** open the link in Chrome, tap the three-dot menu, then *Install app* or *Add to Home Screen*
- **Desktop Chrome or Edge:** use the install icon in the address bar

### Resumes after a refresh

The current case is saved in your browser, and the app offers to continue it the next time you open the page.

---

## Getting started

1. Open the app and choose sex, age, weight and height for a mock patient.
2. Pick a model: **Schnider**, **Marsh** or **Eleveld**. For Eleveld, say whether an opioid was given.
3. Set the induction start time and an initial target effect-site concentration. The start time defaults to 15 minutes ago, and **Use now** sets it to the current time.
4. Tap **Start case**. The suggested rate, chart and countdown appear.
5. Change the target or log boluses as the case evolves. Tap **Reset** (twice) to clear everything and start a new mock patient.

Accepted ranges: age 18 to 100 y, weight 30 to 250 kg, height 120 to 220 cm, target 0.5 to 10 mcg/mL. The app warns when a patient falls outside the range a model was built on, or when BMI is 35 or more.

---

## How the rates are worked out

The engine replays what a TCI pump would infuse to reach and hold the target, then converts it to manual rates:

- A three-compartment model with an effect site, stepped every 10 seconds (the discretisation is exact, using a matrix exponential).
- The initial loading bolus is ignored, so the figures are the infusion rates you would run after induction. Boluses you log yourself are included.
- Rates scale linearly with the target, are rounded to the nearest 10 mcg/kg/min, and fall over time as drug builds up in the tissues.
- After a logged bolus, the rate is reduced so the effect site returns to target within about five minutes.

| Model | Parameters | Effect site (ke0) | Notes |
|---|---|---|---|
| Schnider | Age, weight, height, lean body mass (James) | 0.456 per min | Built on ages 25 to 81, 44 to 123 kg |
| Marsh | Weight only | 1.21 per min | Fixed rate constants, V1 of 0.228 L/kg |
| Eleveld | Age, sex, weight, height, opioid | Scales with weight | General purpose model, also drives the BIS estimate |

The Eleveld BIS estimate uses the published sigmoid pharmacodynamic model, with Ce50 depending on age.

### References

- Schnider TW, et al. *Anesthesiology* 1998;88:1170 and 1999;90:1502
- Marsh B, et al. *Br J Anaesth* 1991;67:41
- Eleveld DJ, et al. *Br J Anaesth* 2018;120:942

---

## Privacy and data

- **No account needed.** Nothing is collected or sent anywhere.
- **All data stays on your device.** The case is stored in your browser's local storage under the key `tciPropofolCase.v1`. Clearing site data erases it.
- The page loads the IBM Plex fonts from Google Fonts. If that is blocked, it falls back to system fonts and works the same.

---

## Frequently asked questions

**Does this talk to a pump?**
No. It is a calculator and teaching aid. It never connects to any device.

**Why do the rates differ from my pump?**
Pumps have their own model implementation, limits and bolus handling, and this app deliberately ignores the loading bolus. Treat the output as an illustration, not a reference.

**Can I use it on a real patient?**
No. It is for education and simulation only.

**Something looks wrong or I have a suggestion.**
Please open an issue at [github.com/GavinOvsak/PropofolModels/issues](https://github.com/GavinOvsak/PropofolModels/issues).

---

## Make your own version

The app itself is one file, [`index.html`](index.html), with no build step, no dependencies and no backend. A few small files around it make it installable and available offline. That makes it easy to fork.

### Host your own copy on GitHub Pages

1. Fork this repository.
2. In your fork, open **Settings, Pages** and set **Source** to **GitHub Actions**.
3. Push to `main` (or run the workflow from the **Actions** tab). The workflow in [`.github/workflows/deploy.yml`](.github/workflows/deploy.yml) copies the site files into a folder and publishes it.
4. Your copy appears at `https://<your-username>.github.io/<repo-name>/`.

To try it locally, open `index.html` in a browser, or run `python3 -m http.server` in the repo folder.

### Where things live in `index.html`

| Section | What it is |
|---|---|
| `<style>` | Design tokens at the top (`:root`), with light and dark themes. Change `--accent` to recolor the app. The theme button saves its choice under `tciPropofolTheme`. |
| `var TCI = (function () {...})()` | The pharmacokinetic engine: `buildModel` holds each model's parameters, `discretise` and `simulate` do the maths. |
| `profile()` and `buildSeries()` | Turn the engine output into the rate per 10 second step, including the bolus adjustment. |
| `render()` and `draw()` | Update the page and draw the SVG chart. |
| `.foam-ribbon` | The #FOAM App corner ribbon. |
| `manifest.webmanifest` | App name, colors and icons used when the app is installed. |
| `sw.js` | The service worker that caches the app for offline use. |
| `icons/` | App icons (SVG sources and PNG exports). |

### Common changes

- **Add a model:** add a branch to `buildModel` that returns `V1, V2, V3, k10, k12, k21, k13, k31, ke0`, then add it to the `#model` dropdown and to `MODEL_NAMES`.
- **Change units or rounding:** `round10` controls the rate step, and the labels are set in the `render()` function.
- **Change the default patient or start time:** edit `DEFAULT_PT` and `DEFAULT_START_OFFSET_MIN` near the top of the main script.
- **Change the saved-case key:** edit `KEY`. Bump the version suffix if you change what is stored, so old saved cases are ignored.
- **Ship an update to installed copies:** the page is fetched fresh whenever the user is online, but if you change `sw.js` or add files to its cache list, bump `VERSION` in `sw.js`.
- **Add a new file to the site:** list it in `SHELL` in `sw.js` and copy it in the deploy workflow's *Stage site* step.
- **Change the app icon:** edit `icons/icon.svg` and `icons/maskable.svg` and export the PNGs at 192, 512 and 180 pixels (the maskable one at 512).
- **Keep or drop the ribbon:** the ribbon links to the FOAMapps directory. Delete the `.foam-ribbon` block if your fork is not part of it, or add your own app to the directory (see below).

### List your app in #FOAM Apps

Add an entry to [`public/data/apps.json`](https://github.com/GavinOvsak/FOAMapps/blob/main/public/data/apps.json) in the FOAMapps repository and open a pull request.

---

## License

Released under the [MIT License](LICENSE). You are free to use, copy, modify, fork and redistribute this app, including in your own projects, as long as the copyright and license notice stay with it. It is provided as is, with no warranty, and is for educational use only (see the notice at the top).

*Built for learners, by a clinician.*
