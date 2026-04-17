# MS.AAD.8 — Guest User Access

**Project:** Entra ID Baselines · **Author:** Areeb  
**Policies:** 3  
[← Back to Overview](./00_Overview.md)

---

## What Is This About?

Guest accounts are useful — they let you collaborate with external partners, contractors, and clients inside your M365 environment. But by default, guests get more access than most people realise. A guest user can enumerate your entire user directory, see group memberships, and in some configurations access resources they were never explicitly invited to.

This group of policies tightens up guest access to the minimum needed for collaboration, and makes sure that external invitations are controlled and scoped to approved domains only.

---

## Policies

| Policy ID | What It Says | Level |
|-----------|-------------|-------|
| MS.AAD.8.1 | Guest users SHOULD have limited access to Entra ID directory objects. | SHOULD |
| MS.AAD.8.2 | Only users with the Guest Inviter role SHOULD be able to invite guest users. | SHOULD |
| MS.AAD.8.3 | Guest invitations SHOULD only be sent to approved external domains. | SHOULD |

---

## MS.AAD.8.1 — Restrict Guest Directory Access

**Why this matters:** A guest user who can browse your full directory can enumerate all your users, see group memberships, and gather intelligence that's useful for a phishing or social engineering campaign — whether they're a malicious insider or a compromised external account.

### How to Apply It

1. Go to [entra.microsoft.com](https://entra.microsoft.com) → **Identity → External Identities → External collaboration settings**

2. Under **Guest user access**, choose one of:
   - `Guest users have limited access to properties and memberships of directory objects` — recommended starting point
   - `Guest user access is restricted to properties and memberships of their own directory objects` — most restrictive, recommended if you don't need guests browsing the directory at all

3. Click **Save**

---

## MS.AAD.8.2 — Restrict Who Can Invite Guests

**Why this matters:** If any user in your org can invite external guests, your guest list grows uncontrolled. People invite personal contacts, old colleagues, and others who have no legitimate business need. Restricting invitations to a designated role means someone with accountability is making that decision.

### How to Apply It

1. Go to **Identity → External Identities → External collaboration settings**

2. Under **Guest invite settings**, select:
   `Only users assigned to specific admin roles can invite guest users`

3. Click **Save**

4. Now assign the **Guest Inviter** role to only the people who legitimately need to bring in external collaborators:
   - Go to **Identity → Roles & admins → Roles & admins**
   - Search for **Guest Inviter**
   - Click **+ Add assignments** and add the appropriate users

---

## MS.AAD.8.3 — Restrict Guest Invitations to Approved Domains

**Why this matters:** Even with invitation restrictions in place, the Guest Inviter role users could still invite people from any domain. Limiting invitations to an approved list ensures guests only come from organisations you've explicitly vetted.

### How to Apply It

1. Go to **Identity → External Identities → External collaboration settings**

2. Under **Collaboration restrictions**, select:
   `Allow invitations only to the specified domains (most restrictive)`

3. Add your approved external domains, one per line:
   ```
   partnercompany.com
   contractorfirm.org
   approvedvendor.com
   ```

4. Click **Save**


### Notes

- Review the approved domain list every quarter and remove any domains that no longer represent active business relationships.
- Changes to invitation settings don't affect existing guest accounts — do a one-time audit of current guests via **Identity → Users**, filtering by User type = Guest, and remove anyone who no longer needs access.
- If you have specific B2B collaboration requirements with a partner tenant (e.g. you want to honour their MFA or device compliance), look into **Cross-Tenant Access Settings** as well — it gives you more fine-grained control than the invitation settings alone.
