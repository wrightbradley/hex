# Self-signing Hex for iPhone

This fork builds with a free Apple ID (Personal Team). The app, keyboard and
widget targets use `com.wrightbradley.hex.*` and the App Group
`group.com.wrightbradley.hex.mobile`, so nothing collides with the upstream
team. `DEVELOPMENT_TEAM` is intentionally unset until Xcode knows your team.

## Once per machine

1. Install Xcode 26.6 or newer (macOS 26.6+):

   ```sh
   xcodes install 26.6 --select
   sudo xcodebuild -license accept
   ```

   `xcodes` prompts for your Apple ID, password and a 2FA code. A free Apple ID
   is enough.

2. Install XcodeGen 2.46.0 or newer: `brew install xcodegen`.

3. Add your Apple ID: Xcode -> Settings -> Accounts -> `+` -> Apple ID. Xcode
   shows it as a Personal Team.

4. On the iPhone, enable Settings -> Privacy & Security -> Developer Mode and
   restart the phone.

## Per checkout

`ios/project.yml` already pins `DEVELOPMENT_TEAM: 53T62XB68X` (the OU field of
the personal certificate). Change it if you sign with a different Apple ID, then
run `xcodegen generate`.

Find the ID in Xcode under Build Settings -> DEVELOPMENT_TEAM, or:

```sh
security find-identity -v -p codesigning
# Apple Development: you@example.com (ABCDE12345)

security find-certificate -c "Apple Development" -p \
  | openssl x509 -noout -subject
# ... OU=TEAMID ...
```

2. Open `ios/HEXMobile.xcodeproj`, pick your iPhone, and Run (Cmd-R). On first
   launch, iOS blocks the app until you trust the certificate: Settings ->
   General -> VPN & Device Management -> Developer App -> trust your Apple ID.
   Then allow Microphone and Speech Recognition, and download a speech model.

3. Enable the keyboard: Settings -> General -> Keyboard -> Keyboards -> Add New
   Keyboard -> Hex, then turn on Allow Full Access. The keyboard and app share
   an App Group, and Full Access is what lets the keyboard read it.

4. Optional: map the Action Button or a Control Center control to Hex's Dictate
   intent. The full keyboard lives under Experiments in the app.

## Limits of free signing

- The provisioning profile expires after 7 days; re-run from Xcode to refresh.
- Up to 3 installed apps and 3 devices per free account.
- 10 App IDs per 7 days. This project uses four (app, keyboard, widgets, tests).
- No TestFlight or App Store distribution with a Personal Team.

If Xcode cannot register the App Group, open each target's Signing &
Capabilities, remove and re-add App Groups, and let Xcode create
`group.com.wrightbradley.hex.mobile`.

If the certificate exists but `security find-identity -v -p codesigning` reports
zero valid identities, the Apple WWDR G3 intermediate is missing. Download
`AppleWWDRCAG3.cer` from <https://www.apple.com/certificateauthority/> and add
it to the login keychain:

```sh
security add-certificates -k ~/Library/Keychains/login.keychain-db AppleWWDRCAG3.cer
```
