# MS.AAD.5 — Application Registration & Consent

**Project:** Entra ID Baselines · **Author:** Areeb  
**Policies:** 4  
[← Back to Overview](./00_Overview.md)

---

## What Is This About?

OAuth consent attacks are one of the most underrated threats in M365. The attack is simple: an attacker creates a convincing-looking app, tricks a user into clicking "Allow", and instantly gains persistent access to that user's email, files, or calendar — no password needed, and the access survives a password reset.

This group of policies closes that door. By restricting who can register apps and grant consent to admins only, you ensure that no rogue application gets a foothold in your tenant without explicit admin review.

---

## Policies

| Policy ID | What It Says | Level |
|-----------|-------------|-------|
| MS.AAD.5.1 | Only administrators SHALL be allowed to register applications. | **SHALL** |
| MS.AAD.5.2 | Only administrators SHALL be allowed to consent to applications. | **SHALL** |
| MS.AAD.5.3 | An admin consent workflow SHALL be configured so users can request app access. | **SHALL** |
| MS.AAD.5.4 | Group owners SHALL NOT be allowed to consent to applications. | **SHALL** |

---

## MS.AAD.5.1 — Admin-Only App Registration

**Why this matters:** When any user can register apps, your tenant fills up with unreviewed, ungoverned registrations — many with excessive permissions and no one monitoring them. Locking this down to admins means every registered app has been through a human review.

### How to Apply It

1. Go to [entra.microsoft.com](https://entra.microsoft.com) → **Identity → Users → User settings**

2. Find **"Users can register applications"**

3. Set it to `No`

4. Click **Save**

---

## MS.AAD.5.2 — Admin-Only App Consent

**Why this matters:** This is the core defence against OAuth phishing. If users can't consent to apps themselves, an attacker's fake app request goes nowhere. Every app that wants access to your users' data has to be reviewed by an admin first.

### How to Apply It

1. Go to **Identity → Applications → Enterprise applications → Consent and permissions → User consent settings**

2. Under **"User consent for applications"**, select:
   `Do not allow user consent`

3. Click **Save**

---

## MS.AAD.5.3 — Admin Consent Workflow

**Why this matters:** Once you've disabled user consent, users still need a way to request access to legitimate apps — without having to email an admin and wait. The consent workflow gives them a self-service request form that lands in an admin queue for review.

### How to Apply It

1. Go to **Identity → Applications → Enterprise applications → Consent and permissions → Admin consent settings**

2. Set **"Users can request admin consent to apps they are unable to consent to"** to `Yes`

3. Under **"Who can review admin consent requests"**, add the users or roles who will handle approvals — typically your Application Administrator or Cloud Application Administrator

4. Optionally enable email reminders to reviewers

5. Click **Save**

> To review pending requests: **Identity → Applications → Enterprise applications → Consent and permissions → Admin consent requests**

---

## MS.AAD.5.4 — Disable Group Owner Consent

**Why this matters:** Group owners are usually regular users, not security-vetted admins. Allowing them to consent to apps on behalf of their group is a consent bypass that attackers can exploit by targeting group owners instead of global admins.

### How to Apply It

1. Go to **Identity → Applications → Enterprise applications → Consent and permissions → User consent settings**

2. Under **"Group owner consent for apps accessing data"**, select:
   `Do not allow group owner consent`

3. Click **Save**


### Notes

- After rolling this out, communicate to users that they can request app access through the new consent workflow — it prevents confusion when they start hitting "access denied" on apps they used to be able to install themselves.
- It's worth doing a one-time audit of existing app consents too. Go to **Identity → Applications → Enterprise applications** and look for any apps with broad permissions like `Mail.Read`, `Files.ReadWrite.All`, or `Directory.ReadWrite.All` — revoke anything that doesn't need that level of access.
