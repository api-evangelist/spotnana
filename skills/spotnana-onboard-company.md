---
name: Onboard a company, its structure, and a traveler
description: Stand up a company, its legal entities/offices, a travel policy, and a traveler on the Spotnana platform.
api: openapi/spotnana-company-openapi-original.json
operations: [createCompany, createLegalEntity, createOffice, createPolicy, createUser]
---

# Onboard a company and a traveler

Authenticate first (see `spotnana-authenticate.md`) and send `Authorization: Bearer <token>`.

## Steps

1. **Create the company** — `createCompany` (`POST /v2/companies`). Returns `companyId`.
2. **Add a legal entity** — `createLegalEntity` (`POST /v2/companies/{companyId}/legal-entities`). Returns `legalEntityId`.
3. **Add an office** — `createOffice` (`POST /v2/companies/{companyId}/legal-entities/{legalEntityId}/offices`).
4. **Optionally add org structure** — cost centers (`createCostCenter`), departments (`createDepartment`), grades (`createGrade`).
5. **Create a travel policy** — `createPolicy` (`POST /v2/companies/{companyId}/policies`) so bookings are policy-controlled.
6. **Create a traveler** — `createUser` (`POST /v2/users`) tied to the company; set travel preferences (`updateTravelPreferences`) and roles (`updateUserRoles`).

## Rules
- Respect the entity hierarchy: Office belongs to LegalEntity belongs to Company (see `data-model/spotnana-data-model.yml`).
- `403` = the API user lacks the RBAC permission for the operation.
- No idempotency key: check `listCompanies` / `queryUser` before re-creating after a failed write to avoid duplicates.
