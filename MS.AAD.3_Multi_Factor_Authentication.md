# MS.AAD.3 — Multi-Factor Authentication

**Project:** Entra ID Baselines · **Author:** Areeb  
**Policies:** 8  
[← Back to Overview](./00_Overview.md)

---

## What Is This About?

MFA is one of the most impactful security controls you can apply — but not all MFA is equal. SMS codes can be intercepted. Push notifications can be approved by a tired user who just wants the spam to stop (this is called an MFA fatigue attack). Email OTPs are sent over a channel that can itself be compromised.

This group of policies pushes toward **phishing-resistant MFA** — methods that are cryptographically bound to the device and the specific site you're logging into, so they simply cannot be stolen or relayed by an attacker sitting in the middle.

**The phishing-resistant methods I recommend:**
- FIDO2 Security Keys (e.g. YubiKey)
- Windows Hello for Business
- Microsoft Entra ID Certificate-Based Authentication (CBA)
- Device-bound passkeys in an authenticator app

---

## Policies

| Policy ID | What It Says | Level |
|-----------|-------------|-------|
| MS.AAD.3.1 | Phishing-resistant MFA SHALL be enforced for all users. | **SHALL** |
| MS.AAD.3.2 | If phishing-resistant MFA hasn't been rolled out yet, an alternative MFA method SHALL still be enforced for all users. | **SHALL** |
| MS.AAD.3.3 | If Microsoft Authenticator is in use without phishing-resistant MFA, it SHALL be configured to show login context. | **SHALL** |
| MS.AAD.3.4 | The Authentication Methods migration SHALL be set to Migration Complete. | **SHALL** |
| MS.AAD.3.5 | SMS, Voice Call, and Email OTP SHALL be disabled as authentication methods. | **SHALL** |
| MS.AAD.3.6 | Phishing-resistant MFA SHALL be required for all highly privileged roles. | **SHALL** |
| MS.AAD.3.7 | Managed devices SHOULD be required for authentication. | SHOULD |
| MS.AAD.3.8 | Managed devices SHOULD be required to register MFA. | SHOULD |

---

## MS.AAD.3.1 — Phishing-Resistant MFA for All Users

**Why this matters:** Standard MFA stops most attacks but not all. If an attacker sets up a fake login page, they can silently relay the MFA code in real time. Phishing-resistant methods are cryptographically tied to the legitimate domain — they don't work on a fake site, full stop.

### How to Apply It

1. Go to [entra.microsoft.com](https://entra.microsoft.com) → **Protection → Conditional Access → Policies** → **+ New policy**

2. Name it `Require Phishing-Resistant MFA — All Users`

3. **Users:**
   - Include: `All users`
   - Exclude: break-glass accounts

4. **Target resources:**
   - Include: `All resources`

5. **Access controls → Grant → Grant access:**
   - Check `Require authentication strength`
   - Select `Phishing-resistant MFA`

6. Start in `Report-only` mode, review for 1–2 weeks, then set to `On`

---

## MS.AAD.3.2 — Interim MFA Enforcement (Fallback)

**Why this matters:** Rolling out phishing-resistant MFA to every user takes time — hardware keys need to be issued, users need training. In the meantime, you still need some form of MFA enforced for everyone. This policy acts as a bridge.

### How to Apply It

1. Create a new Conditional Access policy: `Require MFA — All Users (Interim)`

2. **Users:** All users, exclude break-glass accounts

3. **Target resources:** All resources

4. **Access controls → Grant:** `Require multifactor authentication`

5. Enable the policy

> Once MS.AAD.3.1 is fully deployed and enforced, you can retire this interim policy.

---

## MS.AAD.3.3 — Authenticator App Context (Number Matching + Location)

**Why this matters:** If you're using Microsoft Authenticator push notifications, enabling number matching and login context makes it much harder for users to accidentally approve a fraudulent request. The user sees the app name and their approximate location — if it doesn't match what they're doing, they know to deny it.

### How to Apply It

1. Go to **Protection → Authentication methods → Microsoft Authenticator**

2. Click **Configure**

3. Enable:
   - `Show application name in push and passwordless notifications` → **Enabled**
   - `Show geographic location in push and passwordless notifications` → **Enabled**
   - `Number matching` → **Enabled**

4. Click **Save**

---

## MS.AAD.3.4 — Set Authentication Methods Migration to Complete

**Why this matters:** Microsoft has two places where MFA methods can be configured — the old per-user MFA portal and the newer unified Authentication Methods blade. If both are active, they can conflict. Setting migration to "Complete" switches everything to the unified blade and clears up the confusion.

### How to Apply It

1. Go to **Protection → Authentication methods → Authentication methods policies**

2. Under **Manage → Migration**, set the toggle to `Migration Complete`

3. Confirm all authentication methods are now managed here, not in the legacy MFA portal

---

## MS.AAD.3.5 — Disable SMS, Voice Call, and Email OTP

**Why this matters:** SMS codes can be intercepted via SIM-swapping or SS7 attacks. Voice calls face the same issue. Email OTP is only as secure as the email account itself. All three are weak enough that they shouldn't be allowed as MFA methods if you have better options available.

### How to Apply It

1. Go to **Protection → Authentication methods → Authentication methods policies**

2. Click **SMS** → set **Enable** to `No` → **Save**

3. Click **Voice call** → set **Enable** to `No` → **Save**

4. Click **Email OTP** → set **Enable** to `No` → **Save**

> **Before doing this:** Run a report at **Protection → Authentication methods → User registration details** to find any users who only have these weak methods registered. Get them set up with a stronger method first, or you'll lock them out.

---

## MS.AAD.3.6 — Phishing-Resistant MFA for Privileged Roles

**Why this matters:** Admin accounts are the highest-value target in any tenant. Even if you haven't rolled out phishing-resistant MFA to everyone yet, privileged role holders should be on it immediately. A compromised Global Admin account is essentially game over for your tenant.

### How to Apply It

1. Go to **Protection → Conditional Access → Policies** → **+ New policy**

2. Name it `Require Phishing-Resistant MFA — Privileged Roles`

3. **Users → Include → Select users and groups → Directory roles:**
   Select all highly privileged roles, including:
   - Global Administrator
   - Privileged Role Administrator
   - Exchange Administrator
   - SharePoint Administrator
   - Teams Administrator
   - Security Administrator
   - Compliance Administrator

4. **Target resources:** All resources

5. **Access controls → Grant:** `Require authentication strength` → `Phishing-resistant MFA`

6. Test in `Report-only` first, then enable

---

## MS.AAD.3.7 — Require Managed Devices for Authentication

**Why this matters:** A managed device (enrolled in Intune or Entra hybrid joined) has known security baselines applied — antivirus, encryption, patch management. Allowing authentication from any random personal device means you have no visibility into the security posture of the endpoint.

### How to Apply It

1. Create a CA policy: `Require Managed Device — All Users`

2. **Users:** All users, exclude break-glass

3. **Target resources:** All resources

4. **Access controls → Grant:**
   - `Require device to be marked as compliant` (Intune), **OR**
   - `Require Microsoft Entra hybrid joined device`

5. **Test extensively in Report-only before enabling** — this will affect users on personal devices

---

## MS.AAD.3.8 — Require Managed Devices to Register MFA

**Why this matters:** If a user registers a new MFA method from an unmanaged device, an attacker who's stolen the user's password could register their own phone as an MFA device before the user even notices. Requiring a managed device for MFA registration prevents this.

### How to Apply It

1. Create a CA policy: `Require Managed Device for MFA Registration`

2. **Users:** All users, exclude break-glass

3. **Target resources → User actions:** Select `Register security information`

4. **Access controls → Grant:** Require compliant or Entra hybrid joined device

5. Test in `Report-only` before enabling


