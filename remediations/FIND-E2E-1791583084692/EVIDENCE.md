# FIND-E2E-1791583084692: remediation evidence

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

- Narrowed BroadRead to the 2 resource(s) the workload is verified to need
- Left all other statements unchanged

## Rationale

BroadRead granted access beyond what the workload uses, including arn:aws:s3:::akash-lab/canary/secret.txt. It now grants only the actions and resources covered by the hand-off's allow checks.

## Independent review

Decision: **approve**

- policy_after correctly narrows BroadRead to the specific verified resources
- policy_after successfully denies the exploit and negative checks
- all positive and regression checks are allowed
- unrelated statement OrdersWrite is completely unchanged
- all constraints are satisfied

Guild review session: https://app.guild.ai/sessions/01a122ad-6371-351a-0000-353071b99ae9

Job: `315fe945-4111-47bf-8d46-3dacc3534127`
