# Globo sem Ruído

Hides noisy sections of [globo.com](https://www.globo.com) (currently sports). It's just CSS: no tracking, no data collection.

I wrote it because the extensions I found on the Chrome Web Store either didn't work or engaged in dubious activity.

## Install

**Firefox:** download the `.xpi` from the [latest release](https://github.com/tiagovignatti/globo-sem-ruido/releases/latest), open `about:addons`, click the gear › "Install Add-on From File…" and pick it.

**Chrome:** clone this repo, open `chrome://extensions`, turn on "Developer mode", click "Load unpacked" and pick the folder.

**Safari** (macOS with Xcode): from the repo folder, run
```
xcrun safari-web-extension-converter . --app-name "Globo sem Ruído" \
  --bundle-identifier com.example.globosemruido --macos-only
```
and run the project in Xcode (⌘R). In Safari, enable Settings › Advanced › "Show features for web developers", then Settings › Developer › "Allow unsigned extensions" (resets on restart), turn the extension on in Settings › Extensions, and allow it on globo.com when asked.

## Customize

The hidden sections are CSS rules in `content_script.css`, and `manifest.json` sets which sites they apply to.

To ship a change to Firefox, bump `version` in `manifest.json` and sign it with your [AMO API keys](https://addons.mozilla.org/developers/addon/api/key/):
```
npx web-ext run                        # try it in a temporary profile
WEB_EXT_API_KEY=... WEB_EXT_API_SECRET=... npx web-ext sign --channel=unlisted
```
The signed `.xpi` lands in `web-ext-artifacts/`. With the Ubuntu snap of Firefox, add `--firefox-profile ~/snap/firefox/common/web-ext-profile --profile-create-if-missing --keep-profile-changes` to `web-ext run`, because the snap can't read the default profile in `/tmp`.
