# MS.AAD.2 — Risk-Based Access

**Project:** Entra ID Baselines · **Author:** Areeb  
**Policies:** 3  
**License needed:** Entra ID P2 or Microsoft 365 E5  
[← Back to Overview](./00_Overview.md)

---

## What Is This About?

Microsoft Entra ID constantly analyses sign-in behaviour in the background — looking for things like leaked credentials appearing on the dark web, impossible travel (someone logging in from London and Karachi within minutes), anonymous VPN usage, and other signals that something is off.

This group of policies takes those risk signals and acts on them automatically. Instead of waiting for a human to notice something suspicious, your tenant blocks the compromised account or the sketchy sign-in the moment it's detected.

> **License note:** All three policies in this group require Entra ID P2 (included with M365 E5, or available as an add-on). Without this license, these checks aren't available.

---

## Policies

| Policy ID | What It Says | Level |
|-----------|-------------|-------|
| MS.AAD.2.1 | Users detected as high risk SHALL be blocked. | **SHALL** |
| MS.AAD.2.2 | A notification SHOULD be sent to the administrator when high-risk users are detected. | SHOULD |
| MS.AAD.2.3 | Sign-ins detected as high risk SHALL be blocked. | **SHALL** |

---

## MS.AAD.2.1 — Block High-Risk Users

**Why this matters:** When Entra ID flags a user as high risk — usually because their credentials were found in a breach dump, or because their account is behaving like it's been taken over — you want that account stopped immediately, not hours later when someone reviews a report.

### How to Apply It

1. Go to [entra.microsoft.com](https://entra.microsoft.com) → **Protection → Conditional Access → Policies** → **+ New policy**

2. Name it something like `Block High-Risk Users`

3. **Users:**
   - Include: `All users`
   - Exclude: break-glass accounts

4. **Target resources:**
   - Include: `All resources`

5. **Conditions → User risk:**
   - Set the risk level to `High`

6. **Access controls → Grant:**
   - Select `Block access`

7. Set to `On`

---

## MS.AAD.2.2 — Admin Notification for High-Risk Users

**Why this matters:** Blocking a user is the immediate response — but someone needs to investigate, remediate, and potentially unlock the account. This alert makes sure the right person knows about it right away.

### How to Apply It

1. Go to [entra.microsoft.com](https://entra.microsoft.com) → **Protection → Identity Protection**

2. Click **Users at risk detected alert**

3. Toggle **Alert Level** to `High`

4. Add the email address(es) of whoever should be notified — your security team, SOC, or yourself

5. Click **Save**

---

## MS.AAD.2.3 — Block High-Risk Sign-ins

**Why this matters:** A sign-in can be flagged as high risk even if the user account itself looks fine — for example, an authentication attempt coming from a known malicious IP, a TOR exit node, or showing signs of a real-time phishing relay. These need to be blocked at the moment they happen.

### How to Apply It

1. Go to **Protection → Conditional Access → Policies** → **+ New policy**

2. Name it `Block High-Risk Sign-ins`

3. **Users:**
   - Include: `All users`
   - Exclude: break-glass accounts

4. **Target resources:**
   - Include: `All resources`

5. **Conditions → Sign-in risk:**
   - Set the risk level to `High`

6. **Access controls → Grant:**
   - Select `Block access`

7. Set to `On`


### Notes

- Start by blocking `High` risk only. Once you're comfortable, you can create a second policy that requires MFA for `Medium` risk sign-ins rather than blocking outright.
- If a legitimate user gets blocked, an admin can dismiss the risk and unblock them from: **Protection → Identity Protection → Risky users**
