# MS.AAD.1 — Legacy Authentication

**Project:** Entra ID Baselines · **Author:** Areeb  
**Policies:** 1  
[← Back to Overview](./00_Overview.md)

---

## What Is This About?

Legacy authentication is a catch-all term for older login protocols — things like IMAP, POP3, SMTP AUTH, and Basic Authentication. These were built before MFA existed, so they have no way to enforce it. An attacker with a stolen password can use these protocols to log straight into a mailbox or account, completely bypassing any MFA you've set up.

The fix is straightforward: block these protocols entirely using a Conditional Access policy. Modern apps and clients don't need them anymore, and leaving them open is one of the most common ways M365 tenants get compromised.

---

## Policies

| Policy ID | What It Says | Level |
|-----------|-------------|-------|
| MS.AAD.1.1 | Legacy authentication SHALL be blocked. | **SHALL** |

---

## MS.AAD.1.1 — Block Legacy Authentication

**Why this matters:** Even with MFA turned on across your tenant, a single open legacy auth pathway lets attackers walk right past it. Password spray attacks almost exclusively target legacy protocols because they're so reliable. Blocking them removes an entire category of attack in one policy.

### Things to Check Before You Start

- Identify any apps or devices that still use legacy auth — old scanners, shared mailboxes, Outlook 2013 or earlier, some on-premises apps sending email via SMTP. These need to be migrated or reconfigured before you enforce the block.
- Run the policy in **Report-only** mode for 1–2 weeks first and check the sign-in logs for any legacy auth traffic you weren't aware of.

### How to Apply It

1. Sign in to [entra.microsoft.com](https://entra.microsoft.com) as a Conditional Access Administrator or Global Administrator.

2. Go to **Protection → Conditional Access → Policies** and click **+ New policy**.

3. Give the policy a clear name, e.g. `Block Legacy Authentication`.

4. Under **Users:**
   - Include: `All users`
   - Exclude: your break-glass emergency access accounts

5. Under **Target resources:**
   - Include: `All resources`

6. Under **Conditions → Client apps:**
   - Toggle **Configure** to `Yes`
   - Check only these two options:
     - ✅ Exchange ActiveSync clients
     - ✅ Other clients
   - Leave Browser and Mobile apps and desktop clients unchecked

7. Under **Access controls → Grant:**
   - Select `Block access`

8. Set the policy to `Report-only` first. After reviewing sign-in logs for a week or two with no surprises, switch it to `On`.


### Notes

- SMTP AUTH used by on-premises devices (printers, scanners, line-of-business apps) often needs special handling. Consider migrating these to OAuth 2.0 or using a dedicated send connector instead.
- If you have service accounts or shared mailboxes using IMAP/POP, switch them to modern auth or use Microsoft Graph API access.
