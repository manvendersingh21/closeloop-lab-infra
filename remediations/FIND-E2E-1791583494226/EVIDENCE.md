# FIND-E2E-1791583494226: remediation evidence

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

The BroadRead statement previously allowed data:read on all resources, including protected canary data. By restricting the Resource to 'app/*', we deny access to canary/secret.txt (as required by the negative check) while still allowing data:read on app resources (as required by the positive and regression checks). OrdersWrite was not modified to preserve its intended permissions.

## Independent review

Decision: **approve**

- policy_after correctly narrows access to 'app/*' to deny the exploit while preserving access to 'app/config.json' and 'app/catalog.json'
- unrelated statement 'OrdersWrite' remains unchanged
- all constraints, positive, negative, and regression checks are satisfied

Guild review session: https://app.guild.ai/sessions/01a122b2-c024-351a-0000-40dcaadec44c

Job: `dfe0a7ef-9b75-4a51-938b-b0ede4efa346`
