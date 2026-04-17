# MS.AAD.4 — Logging & Monitoring

**Project:** Entra ID Baselines · **Author:** Areeb  
**Policies:** 1  
[← Back to Overview](./00_Overview.md)

---

## What Is This About?

Entra ID generates a rich stream of security events — every sign-in, every role assignment, every policy change, every risky detection. But by default, those logs only live inside Entra ID for 7–30 days (depending on your license), and they're not being actively monitored by anyone.

This policy is about getting those logs into a SIEM or Log Analytics workspace where they can be retained longer, queried, and used to trigger alerts. Without this, you're essentially flying blind when something goes wrong.

---

## Policies

| Policy ID | What It Says | Level |
|-----------|-------------|-------|
| MS.AAD.4.1 | Security logs SHALL be sent to a security monitoring destination. | **SHALL** |

---

## MS.AAD.4.1 — Forward Logs to a SIEM or Log Analytics

**Why this matters:** When an incident happens, you need logs that go back further than 30 days, and you need them searchable. Forwarding to a SIEM also enables real-time alerting — so you're notified of suspicious activity as it happens, not after the fact.

### What Logs to Export

I recommend exporting all of the following categories:

| Log Type | What It Contains |
|---|---|
| AuditLogs | All directory changes — user creation, role assignments, policy edits |
| SignInLogs | Interactive user sign-ins |
| NonInteractiveUserSignInLogs | Token refreshes and background authentications |
| ServicePrincipalSignInLogs | App and service principal logins |
| ManagedIdentitySignInLogs | Azure managed identity authentications |
| ProvisioningLogs | SCIM provisioning events |
| RiskyUsers | Users flagged by Identity Protection |
| UserRiskEvents | Individual risk detections on user accounts |
| RiskyServicePrincipals | Service principals flagged as at-risk |
| ServicePrincipalRiskEvents | Risk detections on service principals |

### How to Apply It

1. Go to [entra.microsoft.com](https://entra.microsoft.com) → **Identity → Monitoring & health → Diagnostic settings**

2. Click **+ Add diagnostic setting**

3. Give it a name, e.g. `Forward-Logs-To-SIEM`

4. Check all log categories from the table above

5. Choose your destination:

   **If you're using Microsoft Sentinel or Log Analytics:**
   - Check `Send to Log Analytics workspace`
   - Select your subscription and workspace

   **If you're using a third-party SIEM (Splunk, QRadar, etc.):**
   - Check `Stream to an event hub`
   - Set up your Event Hub namespace and connection

   **For long-term archival only:**
   - Check `Archive to a storage account`
   - Set your retention period

6. Click **Save**


### Notes

- Default Entra ID log retention: 7 days (free), 30 days (P1/P2). Forwarding to a SIEM gives you custom retention — typically 90 days to 1 year is recommended.
- If you're using Microsoft Sentinel, also consider enabling the **Microsoft Entra ID** data connector directly, which gives you pre-built analytics rules and workbooks on top of the raw logs.
