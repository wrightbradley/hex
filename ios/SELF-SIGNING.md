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

1. Point the project at your team. Open `ios/project.yml`, add
   `DEVELOPMENT_TEAM: <your team ID>` under `settings: base:`, and run
   `xcodegen generate`.

   Find the ID in Xcode under Build Settings -> DEVELOPMENT_TEAM, or:

   ```sh
   security find-identity -v -p codesigning
   # Apple Development: you@example.com (ABCDE12345)
   ```

2. Open `ios/HEXMobile.xcodeproj`, pick your iPhone, and Run (Cmd-R). Allow
   Microphone and Speech Recognition, and download a speech model.

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
