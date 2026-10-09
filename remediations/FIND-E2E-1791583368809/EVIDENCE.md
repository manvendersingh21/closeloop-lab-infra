# FIND-E2E-1791583368809: remediation evidence

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

- Narrowed the Resource in the BroadRead statement from '*' to 'app/*' to prevent access to canary/secret.txt while preserving access to app resources.
- Left OrdersWrite statement unchanged as required.

## Rationale

The BroadRead statement previously allowed data:read on all resources, including protected canary data. By restricting the Resource to 'app/*', we deny access to canary/secret.txt (as required by the negative check) while still allowing data:read on app resources (as required by the positive and regression checks). OrdersWrite was not modified to preserve existing write permissions.

## Independent review

Decision: **approve**

- Policy after narrows the resource in 'BroadRead' to deny 'canary/secret.txt' (negative check passed)
- All positive and regression checks are allowed under 'app/*'
- Statement 'OrdersWrite' remains unchanged
- All constraints hold and the summary accurately reflects the changes

Guild review session: https://app.guild.ai/sessions/01a122b0-e7e4-351a-0000-ddcd2e11fa79

Job: `f3c8b871-78a0-4d89-ac83-1b136bf3fb7d`
