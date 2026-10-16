# Privacy Policy — Passport

**Effective date:** June 3, 2026
**Last updated:** October 15, 2026
**Contact:** [wyattflew@yahoo.com](mailto:wyattflew@yahoo.com)

---

## Summary

Passport does not collect, transmit, or sell your personal data. Your cryptographic keys are generated on your device and never leave it.

---

## What we collect

**Nothing.**

Passport has no user accounts, no analytics SDK, no crash reporter, and no advertising framework. No personal information is collected by the app or by us.

---

## Your keys and credentials

- Signing keys are generated using your device's Secure Enclave (P-256 / ES256).
- Keys are stored in the Keychain with `kSecAttrAccessibleWhenUnlockedThisDeviceOnly`.
- Keys are not synced to iCloud, not backed up, and do not leave your device.
- Passport metadata (name, scopes, expiry) is stored on your device only.

---

## Verification endpoint

When a third-party service verifies a Passport token, it sends the token to:

```
https://passport-verify.passport-app.workers.dev/verify
```

This endpoint:
- Receives the JWT submitted by the third-party service (not by your device)
- Verifies the cryptographic signature using the public key embedded in the token
- Checks expiry, scope, and whether the credential has been revoked
- Returns a valid/invalid response with the agent name, scopes, and expiry
- Does not store tokens, and keeps no logs, databases, or analytics of its own

The endpoint never sees your private key.

---

## Revocation

When you revoke a passport, its token ID is added to a revocation list so verification fails. The list contains only token IDs and no personal information.

---

## Third-party services

Passport does not integrate any third-party SDKs, advertising networks, or analytics services. The verify endpoint runs on [Cloudflare's infrastructure](https://www.cloudflare.com/privacypolicy/). Cloudflare may log request metadata (IP address, timestamp) under its own privacy policy.

---

## Data retention

We do not store personal data, so there is nothing to retain or delete.

---

## Children

Passport does not knowingly collect information from anyone, including children. The app is rated 4+.

---

## Changes

If this policy changes materially, the dates above will be updated and the new version will be posted on this page.

---

## Contact

Questions: [wyattflew@yahoo.com](mailto:wyattflew@yahoo.com)
