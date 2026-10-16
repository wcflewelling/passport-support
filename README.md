# Passport Support

Passport issues cryptographically signed, revocable credentials to the AI agents that act on your behalf. Keys are generated in your device's Secure Enclave, issuance is gated behind Face ID, and any service can verify a credential with a single API call.

If you don't see your question here, email **[wyattflew@yahoo.com](mailto:wyattflew@yahoo.com)**.

- [Privacy Policy](./privacy.md)

---

## How it works

1. **Create a passport** for each agent and choose what it's allowed to do (scopes) and when it expires.
2. Passport generates an ES256 key pair in the Secure Enclave and signs a JWT. The private key never leaves your device.
3. The agent attaches the JWT to its requests.
4. The receiving service verifies it with one call to the verify endpoint.
5. **Revoke** a passport at any time. Revoked credentials fail verification immediately.

## Verify endpoint

| Endpoint | URL |
|---|---|
| Verify | `https://passport-verify.passport-app.workers.dev/verify` |
| Health | `https://passport-verify.passport-app.workers.dev/health` |

The endpoint checks signature, expiry, scope, and revocation status. It does not store tokens.

## FAQ

**Do I need an account?**
No. Passport has no accounts and no sign-up.

**What happens if I lose my device?**
Keys are bound to that device's Secure Enclave and are not backed up. Issue new passports from your new device.

**Can Passport see what my agents do?**
No. Passport issues credentials; it doesn't observe or log agent activity.

**Which platforms are supported?**
iOS and macOS.

## Contact

- **Email:** [wyattflew@yahoo.com](mailto:wyattflew@yahoo.com)
- When you write in, include your device model, OS version, the Passport app version, and what happened.
