# Attendance Register — Android app (Google Play) built with GitHub

This project turns the **Attendance Register** web app (`www/index.html`) into a real Android app using
[Capacitor](https://capacitorjs.com). GitHub builds the files for you in the cloud, so you do **not** need Android Studio.

What the build produces:

| File | What it is for |
|---|---|
| `….aab` | The file you **upload to Google Play** (required format for new apps) |
| `….apk` | A file colleagues can install directly on a phone (without Play) for quick tests |

The app is free, has no ads, no account and no server. All data stays on the phone. Excel files are saved/sent through
the normal Android **share sheet** (Drive, Telegram, e-mail, Excel…).

> **Status of this project:** the web app and the Android project were tested as far as possible without a phone
> (see "What was and was not tested" at the bottom). The first cloud build is the real test — if a step fails, see
> **Troubleshooting**.

---

## 0. Already done for you

- **Package name** (the app's permanent ID on Google Play) is set to **`bm.attendance`** everywhere in the project
  (`capacitor.config.json`, `android/app/build.gradle`, `strings.xml` and the `MainActivity.java` folder). You do not
  need to change anything. Once you upload to Google Play it can never be changed.
- **Contact e-mail** `716003@gmail.com` is already in `docs/privacy.html`, `PRIVACY_POLICY.md` and `store/LISTING.md`.

---

## 1. Install what you need on your computer (one time)

You already have a GitHub account and **GitHub Desktop**. You also need:

- **Java 21 (JDK)** — only to create the signing key. Download "Temurin 21" from <https://adoptium.net> and install it.
  Check in a terminal (Windows: *PowerShell*): `keytool -help` should print help text.
- Nothing else. (Node.js, Android SDK and Gradle are installed by GitHub in the cloud.)

---

## 2. Create your upload key (one time) — and guard it

The key proves that updates come from you. **Never put it on GitHub as a file, never send it to anyone.**
Back it up (e.g. USB stick + password manager). If you lose it you can ask Google to reset the *upload* key, but it is
a slow process.

In PowerShell (or Terminal on Mac), go to a safe folder, **not** inside the project folder, e.g. `cd ~\Documents`:

```
keytool -genkeypair -v -keystore upload-keystore.jks -alias upload -keyalg RSA -keysize 2048 -validity 10000
```

- Choose a strong password (write it down). Press Enter to reuse it as the key password if asked.
- Name / organisation questions: answer anything sensible (e.g. your name, "Boborahim Mashrab").

Now turn the key file into text so GitHub can store it as a *secret*:

- **Windows (PowerShell):**
  `[Convert]::ToBase64String([IO.File]::ReadAllBytes("upload-keystore.jks")) | Set-Clipboard`
- **Mac:** `base64 -i upload-keystore.jks | pbcopy`
- **Linux:** `base64 -w0 upload-keystore.jks`

(The long text is now in your clipboard — you will paste it in step 4.)

---

## 3. Put the project on GitHub (with GitHub Desktop)

1. Unzip `attendance-register-android.zip` somewhere (e.g. `Documents\attendance-register`).
2. Open **GitHub Desktop** → **File → Add local repository…** → choose that folder.
   If it says "this directory does not appear to be a Git repository", click **create a repository**, keep the defaults,
   press **Create repository**.
3. In the left panel you will see all files. Summary: `Initial version` → **Commit to main**.
4. Click **Publish repository**.
   - Name: `attendance-register`.
   - **Keep this code private**: either works. *Public* is simpler (free GitHub Pages for the privacy policy, and your
     colleagues can download the APK from the Releases page). Your signing key is **not** in the repo, and there is no
     student data in it, so public is safe. Choose *private* if you prefer — then see the notes in steps 7 and 11.
5. Click **Publish repository**.

---

## 4. Add the four secrets

On github.com open your repository → **Settings → Secrets and variables → Actions → New repository secret**.
Create exactly these four (names must match):

| Name | Value |
|---|---|
| `KEYSTORE_BASE64` | the long text from step 2 (paste from the clipboard) |
| `KEYSTORE_PASSWORD` | the keystore password you chose |
| `KEY_ALIAS` | `upload` |
| `KEY_PASSWORD` | the key password (same as the keystore password if you pressed Enter) |

---

## 5. Build the app in the cloud

1. Repository → **Actions** tab (if GitHub asks, click "I understand my workflows, go ahead and enable them").
2. Left list: **Build Android app (AAB for Play Store + APK)** → **Run workflow** → keep `1.0.0` → **Run workflow**.
3. Wait ~5–10 minutes until the run has a green ✔. (First run is the slowest.)
4. Open the run → at the bottom **Artifacts** → download `attendance-register-1.0.0-build1`. Unzip it:
   - `attendance-register-1.0.0-1.aab` → for Google Play
   - `attendance-register-1.0.0-1.apk` → for installing directly

If the run is red ✖, open it, click the red step, and read the error; see **Troubleshooting**.

---

## 6. Try the APK on a real phone before Google Play

1. Send the `.apk` to your phone (Telegram/Drive/USB).
2. Open it; Android asks to allow "install unknown apps" for that app → allow → Install.
3. Test: add a group, add a student, **Generate Excel** → the Android share sheet appears → send to Drive or open in
   Excel → check the front page, group sheet and back page. Also test **Export backup** and rotating the phone.
4. Fix anything now (see section "Changing the app later").

---

## 7. Privacy policy (Google requires a web link)

1. The date and e-mail in `docs/privacy.html` are already filled in. If you ever change them, edit the file, then
   commit + **Push origin** in GitHub Desktop.
2. github.com → repository → **Settings → Pages** → *Build and deployment*: **Deploy from a branch** → branch `main`,
   folder `/docs` → **Save**. After 1–2 minutes the page is at
   `https://YOUR-GITHUB-USERNAME.github.io/attendance-register/privacy.html`.
   (Private repositories need a paid GitHub plan for Pages. Alternative: paste the policy text on any public page you
   control, e.g. your center's website or a public Google Doc/Site, and use that link.)

---

## 8. Create the app in Google Play Console

Open <https://play.google.com/console> → **Create app**.

- App name: `Attendance Register` (max 30 characters). Default language: English (add Uzbek/Russian later if you like).
- App or game: **App**. Free or paid: **Free** (cannot be changed to paid later). Accept the declarations.

Then fill the **Dashboard → Set up your app** tasks (Google changes the wording from time to time; the meaning stays):

| Task | What to answer |
|---|---|
| Privacy policy | the link from step 7 |
| App access | "All functionality is available without special access" |
| Ads | **No**, the app does not contain ads |
| Content rating | Start the questionnaire, category *Utility/Productivity/Education*; answer **No** to violence, etc. |
| Target audience | **18 and over** (it is a tool for teachers; do not choose child age groups) |
| Data safety | "Does your app collect or share user data?" → **No**. (The data stays on the device; nothing is sent to you or others.) |
| Government / financial / health / news apps | **No** to all |
| Category & contact details | Category **Education** (or Productivity); add your e-mail |

**Store listing** — ready-made texts and images are in the `store/` folder:
- `store/LISTING.md` → short description, full description
- `store/icon-512x512.png` → app icon
- `store/feature-graphic-1024x500.png` → feature graphic
- `store/screenshots/*.png` → 4 phone screenshots (1080×1920, sample data only)

(Only use screenshots with **fake** data like these — never real students.)

---

## 9. Upload the AAB and release

Which path depends on your Play Console account type:

**A) Personal account created after November 2023** — Google requires a **closed test** first: at least **12 testers
who stay opted-in for 14 days in a row**, and only then can you apply for Production. Your colleagues are perfect testers.

1. **Testing → Closed testing → Create track** (or use the default "Closed testing").
2. **Create new release** → accept **Play App Signing** → upload `….aab` → release name e.g. `1.0.0` →
   release notes (`First release`) → **Next → Save → Send for review / Publish to closed testing**.
3. **Testers** tab → create an e-mail list with **12 or more colleagues' Google (Gmail) addresses** → save.
   Copy the **opt-in link** and send it to them. Each must open the link, tap **Become a tester**, then install the app
   from Play. They must **stay opted in** for 14 days (they can simply keep the app installed).
4. After 14 days: **Dashboard → "Apply for production"** (answer a few questions about your testing) → when approved:
   **Production → Create new release** → upload the same/newer AAB → publish.

**B) Organisation account** — you can skip the closed-test requirement: **Production → Create new release → upload AAB →
Review → Start rollout to Production.**

Optional quick check first: **Testing → Internal testing** (up to 100 testers, available within minutes, no review) —
good for a first sanity check with 2–3 colleagues.

First reviews can take a few days. When approved, colleagues find the app by searching "Attendance Register" or via the
link you copy from **Dashboard**. It is free.

> Google's rules change. If a screen differs from this guide, the official help is at
> <https://support.google.com/googleplay/android-developer> (search "closed testing" / "create and set up your app").

---

## 10. Changing the app later (new version)

1. Replace `www/index.html` with the new web app file (or edit it).
2. GitHub Desktop → commit → **Push origin**.
3. github.com → **Actions → Run workflow**, version e.g. `1.0.1`. (The `versionCode` goes up automatically.)
4. Play Console → **Production** (or your test track) → **Create new release** → upload the new `.aab`.

Users' saved groups are kept when the app updates (they live inside the app). They are lost if the user uninstalls the
app or clears its storage — remind colleagues to use **Export backup** now and then.

Optional: to get the APK on the repository's *Releases* page automatically, on github.com open **Releases → Draft a
new release → Choose a tag**, type `v1.0.1`, **Create new tag**, **Publish release**. The workflow then runs and
attaches the APK to that release.

---

## 11. Giving the app to colleagues

- **Best:** the Google Play link (updates are automatic).
- **Without Play (quick tests):** the `.apk` (from the workflow artifact, or from **Releases** if the repo is public).
  Colleagues must allow "install unknown apps". An APK installed this way **cannot be updated by Play** later —
  they must uninstall it first (export a backup before!) and install from Play.

---

## Troubleshooting

| Problem | What to do |
|---|---|
| Run fails at "Check signing secrets" | A secret is missing/misspelled. Re-create all four exactly as in step 4. |
| `keytool` not found | Java is not installed or the terminal was opened before installing — reopen the terminal. |
| Fails at "Build signed AAB…" with `Keystore was tampered with, or password was incorrect` | `KEYSTORE_PASSWORD`/`KEY_PASSWORD` is wrong, or the base64 text was cut — redo step 2 and update the secrets. |
| Fails with `SDK location not found` or `license` errors | Re-run the workflow once; if it repeats, open the log and search "Failed to install"; usually a transient download problem. |
| Fails with `Unsupported class file major version` / Java errors | The workflow must use Java 21 (it does). Do not change `java-version`. |
| Play rejects the upload: `Version code already used` | Run the workflow again (the number increases by itself) and upload the new AAB. |
| Play: "You uploaded a debug/unsigned bundle" | You uploaded the wrong file; use the `.aab` produced by this workflow. |
| Play: "Your app targets API level …" | `android/variables.gradle` → `targetSdkVersion` must meet Google's current rule (36 since 31 Aug 2026). Update Capacitor (`npm install @capacitor/core@latest @capacitor/android@latest …`) when Google raises it. |
| Phone: "App not installed" for the APK | Uninstall any older copy first; make sure you copied the whole file. |
| Excel file does not appear after "Generate" | In the share sheet choose Drive/Files/Telegram; or tap **Save / send again** under the message. |
| Top/bottom of the app hidden behind the status or navigation bar | Tell me the phone model + screenshot; the layout uses the safe-area values Capacitor provides. |

---

## How it works (for the curious)

- `www/index.html` — the whole app (HTML+CSS+JS + the Excel template, one file). Works as a normal web page too.
- `capacitor.config.json` — app ID, name, edge-to-edge settings.
- `android/` — the native Android project generated by Capacitor 8 (target API 36, min Android 7.0). Already patched so
  that `versionCode`/`versionName` and the signing key come from the workflow (`android/app/build.gradle`).
- `.github/workflows/android-release.yml` — the cloud build.
- Inside the app: if it runs as an Android app, **Generate Excel** writes the file to the app's cache and opens the
  Android share sheet (Capacitor *Filesystem* + *Share* plugins). In a normal browser it downloads the file as before.
- `docs/` — privacy policy page for GitHub Pages. `store/` — Play Store listing texts and images.

## What was and was not tested before delivery

Tested: the web app in Chromium (groups, themes, Excel generation, migration, backup); the Android share code path with a
simulated Capacitor runtime (the generated `.xlsx` is valid); `npx cap add/sync android` runs cleanly; the Gradle file
patch was reviewed.
**Not tested (no Android SDK available where this was prepared):** the Gradle build itself, installing on a physical
phone, Play Console upload. The first GitHub build is therefore the first real build.
