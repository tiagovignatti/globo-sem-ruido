This extension overrides CSS to customize the look of certain websites.

I wrote it because the extensions I found on the Chrome Web Store either didn't work or engaged in dubious activity.

This is a very simple extension that you can customize for your needs.


# Install
* Open `chrome://extensions/` in the address bar.
* Enable "Developer mode" toggle in top right.
* Click on "Load unpacked"

# Customizing
* Edit `manifest.json` to assing a css file to the website of your choice.
* Modify the css file.
* Reload the extension from `chrome://extensions/`



# Safari
The extension only uses `content_scripts` (no `chrome.*` APIs), so it runs as a Safari Web Extension without code changes. Packaging requires macOS with Xcode.

* Generate an Xcode project (files are referenced, not copied, so edits in this repo still apply after a rebuild):
  ```
  xcrun safari-web-extension-converter /path/to/css-override-chrome-extension \
    --app-name "CSS Override" \
    --bundle-identifier com.example.cssoverride \
    --macos-only
  ```
  Omit `--macos-only` to also get an iOS target.
* Build and run in Xcode (⌘R). This launches the container app that installs the extension.
* In Safari, enable Settings › Advanced › "Show features for web developers".
* In Settings › Developer, check "Allow unsigned extensions" (it resets every time Safari restarts; sign the app with your Apple ID under Signing & Capabilities to avoid this).
* In Settings › Extensions, enable "CSS Override".
* Grant the extension access to the site when prompted ("Always Allow on This Website"), otherwise the CSS is not injected.
* After editing the css or `manifest.json`, rebuild in Xcode.



# Firefox
Release Firefox only installs signed add-ons, so the extension is signed as *unlisted* on addons.mozilla.org (AMO): Mozilla signs it but does not publish it. The `browser_specific_settings.gecko` block in `manifest.json` holds the add-on ID and data collection declaration that signing requires; Chrome ignores it with an "Unrecognized manifest key" warning.

* To try changes, run the extension in a separate profile that reloads it whenever a file changes:
  ```
  npx web-ext run \
    --firefox-profile ~/snap/firefox/common/web-ext-profile \
    --profile-create-if-missing --keep-profile-changes \
    --start-url https://ge.globo.com
  ```
  The profile path is needed for the Ubuntu snap of Firefox, which cannot read the `/tmp` profile `web-ext` uses by default. With other Firefox installs, plain `npx web-ext run` works.
* To install permanently, create API credentials at https://addons.mozilla.org/developers/addon/api/key/ and sign:
  ```
  WEB_EXT_API_KEY=... WEB_EXT_API_SECRET=... npx web-ext sign --channel=unlisted
  ```
  The signed `.xpi` lands in `web-ext-artifacts/`.
* In `about:addons`, click the gear › "Install Add-on From File…" and pick the `.xpi`.
* After editing the css or `manifest.json`, bump `version` in `manifest.json` (AMO will not sign a version twice), sign again and install the new `.xpi` over the old one.
