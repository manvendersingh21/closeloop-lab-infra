# FIND-CYBER-E2E-be0ff446a0484be887ec6f95c937b00e: remediation evidence

**Finding:** Cyber Defense found workload policy exposure to protected canary data
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

- Narrowed BroadRead to the 2 resource(s) the workload is verified to need
- Left all other statements unchanged

## Rationale

BroadRead granted access beyond what the workload uses, including arn:aws:s3:::akash-lab/canary/secret.txt. It now grants only the actions and resources covered by the hand-off's allow checks.

## Independent review

Decision: **approve**

- policy_after correctly narrows resource access to app/config.json and app/catalog.json, denying the canary/secret.txt exploit
- all negative checks are successfully denied
- all positive and regression checks are allowed
- unrelated statement OrdersWrite is unchanged
- constraints are respected and no permissions are widened

Guild review session: https://app.guild.ai/sessions/01a122eb-3195-351a-0000-ecfa63e3b02e

Job: `4b268561-0eea-4547-a4fc-f9f846262da9`
