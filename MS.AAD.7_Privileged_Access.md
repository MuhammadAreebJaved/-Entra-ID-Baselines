# MS.AAD.7 — Privileged Access Management

**Project:** Entra ID Baselines · **Author:** Areeb  
**Policies:** 9  
**License needed:** Entra ID P2 or Microsoft 365 E5 (for PIM)  
[← Back to Overview](./00_Overview.md)

---

## What Is This About?

Admin accounts are the crown jewels of any M365 tenant. A compromised Global Administrator can read every email, delete every user, exfiltrate every file, and lock out the legitimate admins — all within minutes. This group of policies is about making sure those accounts are as hard to abuse as possible.

The main tool here is **Privileged Identity Management (PIM)** — an Entra ID P2 feature that replaces permanent admin role assignments with time-limited, approval-gated, just-in-time access. Instead of being a Global Admin 24/7 (and being a juicy target 24/7), an admin only holds that role for the specific hours they need it, after an approval workflow confirms it's legitimate.

### Highly Privileged Roles (Referenced Throughout This Document)

- Global Administrator
- Privileged Role Administrator
- Exchange Administrator
- SharePoint Administrator
- Teams Administrator
- Security Administrator
- Compliance Administrator
- Application Administrator
- Cloud Application Administrator
- Hybrid Identity Administrator
- User Administrator
- Directory Writers

Apply all policies in this group to every role on this list, not just Global Administrator.

---

## Policies

| Policy ID  | What It Says                                                                                | Level     |
| ---------- | ------------------------------------------------------------------------------------------- | --------- |
| MS.AAD.7.1 | Between 2 and 8 users SHALL hold the Global Administrator role — no more, no less.          | **SHALL** |
| MS.AAD.7.2 | Privileged users SHALL use scoped roles instead of Global Administrator where possible.     | **SHALL** |
| MS.AAD.7.3 | Privileged accounts SHALL be cloud-only — not synced from on-premises AD.                   | **SHALL** |
| MS.AAD.7.4 | Permanent active role assignments SHALL NOT exist for highly privileged roles.              | **SHALL** |
| MS.AAD.7.5 | All privileged role grants SHALL go through a PAM system (PIM) — not assigned directly.     | **SHALL** |
| MS.AAD.7.6 | Activating the Global Administrator role SHALL require approval from a designated reviewer. | **SHALL** |
| MS.AAD.7.7 | Any eligible or active assignment to a highly privileged role SHALL trigger an alert.       | **SHALL** |
| MS.AAD.7.8 | Any activation of the Global Administrator role SHALL trigger an alert.                     | **SHALL** |
| MS.AAD.7.9 | Activation of other highly privileged roles SHOULD trigger an alert.                        | SHOULD    |

---

## MS.AAD.7.1 — Keep Global Admin Count Between 2 and 8

**Why this matters:** One Global Admin is a single point of failure — if they leave or get locked out, you can't recover. More than eight means the attack surface is unnecessarily large and oversight becomes difficult.

### How to Apply It

1. Go to [entra.microsoft.com](https://entra.microsoft.com) → **Identity → Roles & admins → Roles & admins**

2. Search for **Global Administrator** and click on it

3. Count every user in the **Active** and **Eligible** assignment lists (including group-based assignments)

4. If under 2: create a dedicated cloud-only backup admin account and assign Global Administrator via PIM as Eligible

5. If over 8: remove excess assignments — reassign those users to more specific scoped roles (see MS.AAD.7.2)

---

## MS.AAD.7.2 — Use Least-Privilege Scoped Roles

**Why this matters:** Most people with Global Administrator don't actually need it for their daily tasks. An Exchange admin only needs to manage Exchange. A user admin only needs to manage users. Giving them Global Admin is handing someone a master key when they only need the bathroom key.

### How to Apply It

1. For each current Global Admin, identify what they actually do

2. Map their tasks to a scoped role:

   | Task | Better Role |
   |---|---|
   | Manage Exchange / email | Exchange Administrator |
   | Manage SharePoint / OneDrive | SharePoint Administrator |
   | Manage Teams settings | Teams Administrator |
   | Manage users and groups | User Administrator |
   | Manage security policies | Security Administrator |
   | Manage compliance settings | Compliance Administrator |
   | Register/manage apps | Application Administrator |

3. Assign the appropriate scoped role via PIM (as Eligible), then remove Global Admin

4. Keep only 2–8 accounts with Global Administrator per policy MS.AAD.7.1

---

## MS.AAD.7.3 — Privileged Accounts Must Be Cloud-Only

**Why this matters:** If an admin account is synced from on-premises AD, then compromising your on-premises domain also compromises your cloud admin. Cloud-only admin accounts break that dependency — on-prem can be breached without handing over the keys to the cloud.

### How to Apply It

1. For each highly privileged user, check whether their account is synced:

   Go to the user's profile in Entra admin center → look for **"On-premises sync enabled"** — it must say `No`

   Or use PowerShell:
   ```powershell
   $gaRole = Get-MgDirectoryRole -Filter "displayName eq 'Global Administrator'"
   Get-MgDirectoryRoleMember -DirectoryRoleId $gaRole.Id | ForEach-Object {
       $user = Get-MgUser -UserId $_.Id -Property DisplayName,OnPremisesImmutableId,UserPrincipalName
       [PSCustomObject]@{
           Name     = $user.DisplayName
           UPN      = $user.UserPrincipalName
           IsOnPrem = $null -ne $user.OnPremisesImmutableId
       }
   }
   ```

2. If any privileged user is synced (`IsOnPrem = True`), create a new dedicated cloud-only admin account:
   - Create it directly in Entra ID — do not sync from AD
   - Use a distinct naming pattern, e.g. `admin.firstname@yourdomain.com`
   - Assign the required privileged role via PIM
   - Remove the role from the synced account

---

## MS.AAD.7.4 — No Permanent Active Role Assignments

**Why this matters:** A permanent role assignment means an attacker who compromises the account has admin access around the clock. With PIM, the role is only active for a few hours at a time, so the window of exposure is tiny.

### How to Apply It

1. Go to **Identity Governance → Privileged Identity Management → Microsoft Entra roles**

2. Click each highly privileged role → **Assignments → Active assignments**

3. For every user with a permanent active assignment, click the three dots (⋯) → **Update**

4. Change **Assignment type** to `Eligible` and set an expiration date

5. Save and repeat for all highly privileged roles

> **Exception:** Break-glass emergency accounts should remain permanently active as Global Administrators. This is intentional — they're your last resort if PIM and CA break down.

---

## MS.AAD.7.5 — All Role Assignments Must Go Through PIM

**Why this matters:** Assigning roles directly in the Entra admin center or via PowerShell bypasses PIM entirely — no approval workflow, no time limit, no alert. Over time this creates a shadow set of privileged accounts that aren't governed by any of your policies.

### How to Apply It

1. All future privileged role grants must be made through: **Identity Governance → Privileged Identity Management → Microsoft Entra roles → Assignments → + Add assignments**

2. Check for existing out-of-band assignments: compare the **Roles & admins** page against the **PIM assignments** list. Any user showing up in one but not the other should be investigated and corrected.

3. Document this as an organisational process — everyone who manages admin accounts must know to use PIM, not the direct assignment UI.

---

## MS.AAD.7.6 — Require Approval to Activate Global Administrator

**Why this matters:** Requiring a second person to approve a Global Admin activation means an attacker with stolen credentials can't just activate the role themselves — someone has to greenlight it. It also gives your team visibility into every activation attempt, including suspicious ones.

### How to Apply It

1. Go to **Identity Governance → Privileged Identity Management → Microsoft Entra roles**

2. Click **Global Administrator → Settings**

3. Under the **Activation** tab:
   - **Require approval to activate:** toggle to `Yes`
   - **Select approvers:** add at least one designated approver (e.g. your security lead or CISO)
   - Set a reasonable **Maximum activation duration** (e.g. 8 hours)

4. Click **Update**

---

## MS.AAD.7.7 — Alert on Any Privileged Role Assignment

**Why this matters:** Unauthorised role assignments are one of the clearest signs of a tenant compromise in progress. You want to know the moment any highly privileged role assignment is made — whether it's legitimate or not.

### How to Apply It

1. Go to **Identity Governance → Privileged Identity Management → Microsoft Entra roles**

2. For each highly privileged role, click on the role → **Settings → Notifications** tab

3. Enable:
   - **Send notification when eligible assignment is added** → add your security team's email
   - **Send notification when active assignment is added** → add your security team's email

4. Click **Update** and repeat for every highly privileged role

---

## MS.AAD.7.8 — Alert on Global Administrator Activation

**Why this matters:** Every time someone activates Global Administrator, that event deserves attention. Knowing it happened — and by whom, and when — is essential for both detection and auditing.

### How to Apply It

1. Go to **Identity Governance → Privileged Identity Management → Microsoft Entra roles → Global Administrator → Settings**

2. Under the **Notifications** tab:
   - **Send notification when a member activates this role:** toggle to `Yes`
   - Add your security team or SOC email as the recipient

3. Click **Update**

---

## MS.AAD.7.9 — Alert on Other Privileged Role Activations

**Why this matters:** Global Admin isn't the only dangerous role. An attacker with Exchange Administrator can access every mailbox. A compromised User Administrator can reset passwords and take over accounts.

### How to Apply It

Repeat the same notification configuration from MS.AAD.7.8 for all other roles in the highly privileged roles list.

Or set up a Sentinel analytics rule to catch all PIM activations in one place:
```kql
AuditLogs
| where OperationName == "Add member to role completed (PIM activation)"
| project TimeGenerated, InitiatedBy, TargetResources
| order by TimeGenerated desc
```

