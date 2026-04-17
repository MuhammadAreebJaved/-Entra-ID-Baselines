# Entra ID Baselines

**Author:** Credit Goes to CISA  
**Last Updated:** April 2026  
**Version:** 1.0

---

## What Is This?

This is my personal documentation project covering Microsoft Entra ID security baselines — a set of identity hardening policies I've researched, tested, and documented for securing Microsoft 365 environments.

The goal is simple: give any M365 administrator a clear, no-nonsense reference for locking down Entra ID. Each policy group has its own file with an explanation of *why* it matters and *exactly* how to apply it — no vague guidance, just step-by-step instructions you can follow in the Entra admin center.

These baselines are applicable to any organization running Microsoft 365, from small businesses to enterprise.

---

## Who Is This For?

- M365 administrators setting up or hardening a tenant
- Security engineers reviewing identity posture
- IT teams wanting a structured checklist for Entra ID configuration
- Anyone preparing for a security audit or compliance review

---

## Policy Groups

| # | File | Topic | Policies |
|---|------|-------|----------|
| 1 | [MS.AAD.1 — Legacy Authentication](./MS.AAD.1_Legacy_Authentication.md) | Block old auth protocols that bypass MFA | 1 |
| 2 | [MS.AAD.2 — Risk-Based Access](./MS.AAD.2_Risk_Based_Access.md) | Automatically block compromised users and risky sign-ins | 3 |
| 3 | [MS.AAD.3 — Multi-Factor Authentication](./MS.AAD.3_Multi_Factor_Authentication.md) | Enforce strong, phishing-resistant MFA for all users | 8 |
| 4 | [MS.AAD.4 — Logging & Monitoring](./MS.AAD.4_Logging_Monitoring.md) | Forward security logs to a SIEM for monitoring | 1 |
| 5 | [MS.AAD.5 — Application Registration](./MS.AAD.5_Application_Registration.md) | Lock down OAuth app consent and app registration | 4 |
| 6 | [MS.AAD.6 — Password Management](./MS.AAD.6_Password_Management.md) | Stop forcing password expiration — it backfires | 1 |
| 7 | [MS.AAD.7 — Privileged Access](./MS.AAD.7_Privileged_Access.md) | Control and monitor admin roles using PIM | 9 |
| 8 | [MS.AAD.8 — Guest User Access](./MS.AAD.8_Guest_User_Access.md) | Restrict what external guests can see and do | 3 |

**Total: 30 policies across 8 groups**

---

## How to Read These Files

Each policy group file follows the same structure:

- **Overview** — what the group is about and why it matters
- **Policy table** — a quick summary of every policy and its requirement level
- **Per-policy section** — the rationale and full step-by-step implementation instructions

### Requirement Levels

| Level | Meaning |
|-------|---------|
| **SHALL** | Must be implemented — no exceptions |
| **SHOULD** | Strongly recommended — document if skipped |

---

## License Requirements

Some policies need higher Microsoft licensing. Here's the breakdown:

| What You Need | Required For |
|---|---|
| Microsoft 365 E3 or equivalent | Most policies in this guide |
| Entra ID P2 or Microsoft 365 E5 | Risk-based Conditional Access (MS.AAD.2) and Privileged Identity Management (MS.AAD.7) |
| Microsoft Intune | Managed device policies (MS.AAD.3.7, MS.AAD.3.8) |

---

## Key Terms

**Conditional Access (CA):** Entra ID feature that enforces access rules based on user, device, location, and risk. Most policies in this guide are applied through CA policies.

**PIM (Privileged Identity Management):** Enables just-in-time role activation — admins only hold elevated permissions when they need them, for a limited window, with approval required.

**Break-Glass Accounts:** Emergency admin accounts that should be excluded from most CA policies to prevent tenant lockout in a crisis. Always exclude these when creating new policies.

**Phishing-Resistant MFA:** Authentication methods that cannot be intercepted by a phishing site — FIDO2 keys, certificate-based auth, Windows Hello for Business. Much stronger than SMS or push notifications.

---

*— Areeb*
