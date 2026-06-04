# Passport
 
Cryptographically signed identity passports for AI agents. Issued on-device by the owner, verified by any website over a public REST API.
Cryptographically signed identity credentials for AI agents. Issued on-device via Secure Enclave, verified by any service over a live REST API.
 
## What this repo currently contains
## What this is
 
The iOS app and all App Store submission assets. You can open it in Xcode today, run it on simulator, create a passport (Ed25519 keypair generated and stored in Keychain), sign a JWT, and verify it locally. Submission to App Store Connect is unblocked — see `AppStore/submission-checklist.md`.
AI agents act on your behalf — booking travel, placing orders, reading your inbox. The services they interact with have no way to know whether the action was actually authorized by you. Passport fixes that.
 
- 4-tab SwiftUI app shell (Dashboard / Create / Verify / Settings)
- Ed25519 keygen via `CryptoKit`, private key stored in Keychain (this-device-only, when-unlocked)
- JWT (EdDSA / Ed25519) builder with passport claims
- CoreData persistence for passport metadata and a local activity log
- Entitlements wired for CloudKit, push notifications, Sign in with Apple, and Keychain sharing
- 1024×1024 app icon, branded launch screen, `PrivacyInfo.xcprivacy` privacy manifest
- App Store listing copy (name, subtitle, description, keywords) within Apple's character limits
- 10 marketing screenshots (5 frames × two device sizes: 6.9" and 6.5")
- TestFlight + App Store submission checklist with export-compliance and reviewer-notes copy
You create a named passport for each agent and choose what it's allowed to do (scopes). The app generates an ES256 keypair in the iPhone's Secure Enclave, protected by Face ID. The agent attaches the resulting JWT to every request. Any store or service verifies it with a single API call.
 
## Setup (one time)
## Live infrastructure
 
The project is generated with [XcodeGen](https://github.com/yonaskolb/XcodeGen) so the `.xcodeproj` stays out of git and never produces merge conflicts.
| Endpoint | URL |
|---|---|
| Verify | `https://passport-verify.passport-app.workers.dev/verify` |
| Health | `https://passport-verify.passport-app.workers.dev/health` |
 
1. **Install XcodeGen** (one-time, requires Homebrew):
The verify endpoint is a Cloudflare Worker — see `worker/` for source and `worker/INTEGRATE.md` for integration guide.
 
   ```bash
   brew install xcodegen
   ```
## iOS app
 
2. **Set your Apple Developer Team ID.** Open `project.yml` and replace `REPLACE_WITH_TEAM_ID` with your 10-character team ID. You can find it at [developer.apple.com → Membership](https://developer.apple.com/account#MembershipDetailsCard).
4-tab SwiftUI app (Dashboard / Create / Verify / Settings):
 
3. **Generate the Xcode project:**
- ES256 JWT issuance via Secure Enclave / Face ID
- Keys stored with `kSecAttrAccessibleWhenUnlockedThisDeviceOnly` — never leave the device
- CoreData persistence for passport metadata
- Local verification (signature + expiry + revocation)
- Tappable passport detail view with copy/revoke/delete
- How To guide in Settings
- App Store demo mode toggle for screenshots
 
   ```bash
   xcodegen
   ```
 
   This creates `Passport.xcodeproj`. Open it:
 
   ```bash
   open Passport.xcodeproj
   ```
 
4. **Register the bundle ID** at developer.apple.com:
   - Identifier: `com.passport.Passport` (or change `PRODUCT_BUNDLE_IDENTIFIER` in `project.yml`)
   - Capabilities: Sign in with Apple, Push Notifications, iCloud (CloudKit), Keychain Sharing
 
5. **Build and run** to a simulator (⌘R). To run on a real device you'll need a provisioning profile that includes the capabilities above.
 
## Don't have XcodeGen? Alternative path
 
If you'd rather not install XcodeGen:
 
1. In Xcode: File → New → Project → iOS → App. Product name: `Passport`. Interface: SwiftUI. Storage: Core Data. Min deployment: iOS 17.
2. Delete the default `ContentView.swift`, `PassportApp.swift`, asset catalog, and entitlements.
3. Drag the entire `Passport/` directory from this repo into the project navigator (check "Copy items if needed" off, "Create groups" on).
4. In project settings → Signing & Capabilities, add: Sign in with Apple, Push Notifications, iCloud (with CloudKit), Keychain Sharing (group: `com.passport.Passport`).
 
## Project layout
 
```
Passport/
├── project.yml                     # XcodeGen spec — edit to change bundle ID, team, deployment target
├── AppStore/                       # Everything needed for App Store Connect submission
│   ├── listing.md                  # Name, subtitle, description, keywords, age rating, export compliance
│   ├── submission-checklist.md     # Step-by-step TestFlight → review → release
│   ├── export-options.plist        # `xcodebuild -exportArchive` config for CI
│   └── screenshots/
│       ├── 6.9/                    # 1320×2868 (iPhone 16 Pro Max)
│       └── 6.5/                    # 1242×2688 (iPhone 11 Pro Max fallback)
├── Passport/
│   ├── App/
│   │   ├── PassportApp.swift       # @main entry point
│   │   └── ContentView.swift       # TabView root
│   │   ├── PassportApp.swift
│   │   ├── ContentView.swift
│   │   └── Theme.swift
│   ├── Views/
│   │   ├── DashboardView.swift     # List of issued passports
│   │   ├── CreatePassportView.swift# New passport wizard (name + scopes)
│   │   ├── VerifyView.swift        # Paste a token or scan QR to verify
│   │   └── SettingsView.swift      # Account, appearance, debug
│   │   ├── DashboardView.swift
│   │   ├── PassportDetailView.swift
│   │   ├── CreatePassportView.swift
│   │   ├── VerifyView.swift
│   │   ├── SettingsView.swift
│   │   └── HowToView.swift
│   ├── Crypto/
│   │   ├── PassportKeyManager.swift# Ed25519 keygen + Keychain wrapper
│   │   └── JWTBuilder.swift        # EdDSA JWT signing
│   │   ├── PassportKeyManager.swift
│   │   └── JWTBuilder.swift
│   ├── Models/
│   │   ├── Passport.swift          # Domain model (struct)
│   │   └── PassportClaims.swift    # JWT claim payload
│   │   ├── Passport.swift
│   │   └── PassportClaims.swift
│   ├── Persistence/
│   │   └── PersistenceController.swift # Core Data stack
│   ├── Networking/
│   │   └── VerifyAPI.swift         # Stub — wired in Phase 4
│   └── Resources/
│       ├── Info.plist
│       ├── Passport.entitlements
│       ├── PrivacyInfo.xcprivacy   # App Store privacy manifest
│       ├── Assets.xcassets/        # AppIcon (1024×1024), AccentColor, LaunchLogo
│       ├── Preview Content/
│       └── Passport.xcdatamodeld/  # Core Data schema
│   │   ├── PersistenceController.swift
│   │   ├── PassportModel.swift
│   │   └── PassportEntity+CoreData.swift
│   └── Networking/
│       └── VerifyAPI.swift
├── worker/
│   ├── src/index.js        # Cloudflare Worker — POST /verify, GET /health
│   ├── wrangler.toml
│   └── INTEGRATE.md        # Store integration guide with Node.js / Python / cURL snippets
├── reference-verifier/
│   └── verify_passport.py  # Python reference implementation
├── AppStore/
│   ├── listing.md
│   └── submission-checklist.md
└── SPEC.md
```
 
## What's next
## Phases
 
| Phase | Status |
| --- | --- |
| 1. Xcode project + SwiftUI tabs | done |
| 2. CryptoKit keygen + Keychain | done |
| 3. JWT token builder | done |
| 4. Backend Verify API (Node/Fastify on Railway) | not started |
| 5. QR code display + Share Sheet | not started |
| 6. Activity log + push notifications | not started |
| 7. App Store submission assets (icon, privacy manifest, screenshots, listing, checklist) | done — see `AppStore/` |
| 8. Submit to TestFlight + App Store review | manual — follow `AppStore/submission-checklist.md` |
|---|---|
| SwiftUI app + Core Data | Done |
| ES256 keygen + Secure Enclave | Done |
| JWT builder + local verify | Done |
| Cloudflare Worker verify endpoint | Done — live |
| App Store submission | Pending |
| Revocation list (Cloudflare KV) | Not started |
| Custom domain (passport.app) | Not started |
| QR code display + Share Sheet | Not started |
 
## Worker — deploy your own
 
```bash
cd worker
npm install
npm run deploy   # requires wrangler login first
```
 
## Security notes
 
- Private keys are generated with `Curve25519.Signing.PrivateKey` and stored in the Keychain with `kSecAttrAccessibleWhenUnlockedThisDeviceOnly`. They never leave the device and are not synced to iCloud.
- Public keys and passport metadata are safe to sync via CloudKit (added in a later phase).
- The JWT signing path is `EdDSA` (RFC 8037). Verifiers should reject any token with `alg != "EdDSA"` and require the `kid` (key ID) header to match a registered public key.
- Revocation lives server-side: a verifier hitting `/verify/:token` must check the revocation list before trusting the signature.
- Private keys are generated in the Secure Enclave using P-256 / ES256. They never leave the device.
- JWT signing uses ECDSA with SHA-256 (ES256). Verifiers reject any token with `alg != "ES256"`.
- The public key is embedded in the JWT header (`jwk` field). Verifiers import it via Web Crypto and verify the signature — no key distribution needed.
- The verify endpoint is stateless: it checks signature, expiry, optional scope, and optional key pin. No data is stored server-side.
- Revocation (Phase 5) will push revoked JTIs to a Cloudflare KV namespace.
 
## Privacy
 
No backend, no accounts, no analytics. See `PRIVACY.md`.
