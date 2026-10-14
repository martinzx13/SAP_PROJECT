# AGENTS.md — SAP IAS + Entra ID SSO POC

## Project Overview

**POC**: Single Sign-On for SAP S/4HANA Fiori via SAP Cloud Identity Services (IAS) with Microsoft Entra ID as Corporate IdP.

**Scope**: SP-initiated SAML 2.0 flow — `User → Fiori → IAS → Entra ID (MFA) → IAS → S/4HANA`. No BTP, no IAM/IAG/IPS, no auto-provisioning/role assignment.

**Leader**: Daniel Egas (requested executable config plan, not high-level activities)

## Current State

| Item | Status |
|------|--------|
| IAS Tenant | Exists (URL: `[PENDING]`) |
| Entra ID Tenant | Exists (Tenant ID: `[PENDING]`) |
| S/4HANA System(s) | `[PENDING]` SID(s), Client(s), Environment count |
| Architecture | Confirmed: Fiori SSO via IAS + Entra ID |
| Correlation | Logon Alias (attribute TBD: `edgar.chacon` vs `edgar.chacon@domain`) |
| Original Plan | 6 high-level activities (too generic) |
| Deliverable | Executive config table with columns: Workstream, TimeBox, Nombre de tarea, Descripción, Status, Responsable, Tiempo Estimado (min), Fecha inicio, Fecha fin, Observación |

## Key Decisions

1. **No BTP** — POC targets S/4HANA Fiori directly
2. **No IAM/IAG/IPS** — Not in scope unless explicitly added
3. **No auto-provisioning** — User must exist in S/4HANA with correct Logon Alias
4. **SAML 2.0** — Not OIDC (Fiori/SAML2 standard)
5. **SP-initiated** — User hits Fiori URL first
6. **MFA via Entra ID CA** — Corporate policy enforced at IdP

## Pending Clarifications

- [ ] Number of environments (DEV/QAS/PRD) and mandants per system
- [ ] Technical owners: IAS Admin, Entra ID Admin, Basis contact
- [ ] Exact Logon Alias attribute mapping decision
- [ ] Certificate strategy (IAS-managed vs customer-managed)
- [ ] Routing rule logic (all users → Entra ID? Domain-based? Group-based?)
- [ ] Test user list in each system
- [ ] Target dates for each environment

## How to Use the Expert Agent

Invoke **`my_agents/SAP_IDENTITY_FEDERATION_EXPERT.md`** with this project context. The agent will:

1. Read this `AGENTS.md` for project-specific constraints
2. Generate the executive configuration plan table
3. Cite SAP Help Portal / Microsoft Learn / SAP Learning docs per workstream
4. Flag all `[PENDING]` items requiring stakeholder input

## Invocation Pattern

```markdown
# Input to SAP_IDENTITY_FEDERATION_EXPERT
Project Context: [this AGENTS.md content]
Request: Generate the executive configuration plan table per column spec
```

## Related Files

- `my_agents/SAP_IDENTITY_FEDERATION_EXPERT.md` — Reusable expert agent (Fabric Pattern)
- `000_System/001_Inbox/` — Raw inputs (original plan image, meeting notes)
- `600_Atlas/` — Future MOC linking this POC to Identity Federation patterns

## Safety & Governance

- Do not invent tenant URLs, certificate thumbprints, user counts, or dates
- Use `[PENDING]` placeholders for all unconfirmed values
- Never commit secrets (certificates, client secrets, passwords)
- All config steps must be traceable to official SAP/Microsoft documentation