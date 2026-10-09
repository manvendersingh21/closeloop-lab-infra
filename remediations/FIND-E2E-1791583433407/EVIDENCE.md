# FIND-E2E-1791583433407: remediation evidence

**Finding:** Workload policy exposes protected canary data on Akash lab-api
**Severity:** high

## Exploit replay

- Before the fix: **success**
- After the fix: **blocked**

## Verification checks

| check_id | phase | expected | actual | passed | detail |
| --- | --- | --- | --- | --- | --- |
| exploit-replay | baseline | deny | allow | false | GET /data/canary/secret.txt → 200, 200, 200 — exploit reproduced live |
| neg-canary-read | live | deny | deny | true | GET /data/canary/secret.txt → 403, 403, 403 |
| pos-app-read | live | allow | allow | true | GET /data/app/config.json → 200, 200, 200 |
| pos-orders-write | live | allow | allow | true | POST /orders → 201, 201, 201 |
| reg-catalog-read | live | allow | allow | true | GET /data/app/catalog.json → 200, 200, 200 |
| exploit-replay | live | deny | deny | true | GET /data/canary/secret.txt → 403, 403, 403 |

## Change summary

- Narrowed the Resource in the BroadRead statement from '*' to 'app/*' to prevent access to canary/secret.txt while preserving access to app/* resources.
- Left OrdersWrite statement unchanged as required.

## Rationale

The original BroadRead statement allowed data:read on all resources, including protected canary data. By restricting the Resource to 'app/*', we deny access to canary/secret.txt (as required by the negative check) while still allowing data:read on app/* (as required by the positive and regression checks). OrdersWrite was not modified to preserve existing functionality.

## Independent review

Decision: **approve**

- policy_after correctly narrows access to 'app/*' to deny the exploit and 'neg-canary-read' check while maintaining 'pos-app-read' and 'reg-catalog-read'
- no permissions were widened and unrelated statement 'OrdersWrite' is untouched
- all constraints and checks are fully satisfied

Guild review session: https://app.guild.ai/sessions/01a122b1-d106-351a-0000-bad8446f21d8

Job: `dc5a5a7d-7bb4-4cc1-94f3-07d26c6968ed`
