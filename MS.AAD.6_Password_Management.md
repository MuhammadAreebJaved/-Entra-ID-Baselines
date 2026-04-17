# MS.AAD.6 — Password Management

**Project:** Entra ID Baselines · **Author:** Areeb  
**Policies:** 1  
[← Back to Overview](./00_Overview.md)

---

## What Is This About?

For years, the conventional wisdom was "force users to change their passwords every 90 days." The thinking made sense in theory — if a password gets stolen, it'll expire soon anyway. In practice it completely backfires.

When users are forced to change passwords regularly, they stop trying to make good ones. They pick `Summer2024!`, then `Autumn2024!`, then `Winter2025!`. Security researchers have documented this pattern extensively. The forced rotation creates predictability, not security.

Modern guidance is clear: **non-expiring passwords combined with breach detection and strong MFA are far more effective** than periodic rotation. If a password gets compromised, MS.AAD.2.x will catch it automatically. There's no need to also annoy every user in the org every 90 days.

---

## Policies

| Policy ID | What It Says | Level |
|-----------|-------------|-------|
| MS.AAD.6.1 | User passwords SHALL NOT expire. | **SHALL** |

---

## MS.AAD.6.1 — Turn Off Password Expiration

**Why this matters:** Forced expiration doesn't reduce breach risk when MFA and risk-based detection are in place. Removing it reduces friction and nudges users toward creating stronger, more memorable passwords they won't cycle through predictably.

### For Cloud-Only Accounts (M365 Admin Center)

1. Open [admin.microsoft.com](https://admin.microsoft.com)

2. Go to **Settings → Org settings → Security & privacy → Password expiration policy**

3. Check **"Set passwords to never expire (recommended)"**

4. Click **Save**

### Notes

- Non-expiring passwords don't mean users *can't* change them — they still can, and should if they suspect their account has been compromised.
- This policy works best when MS.AAD.2.x (risk-based blocking) is also in place. Together they handle the "what if a password does get stolen" concern without forcing everyone to rotate on a schedule.
