# AffixIO evaluation: GoogleCloudPlatform/contact-center-ai-samples
Commit 386e404 (upstream main). Run 12 Oct 2026. Policy: articus-spec/SPEC-v2.md.

## 1. Verdict
**NO PR, for either the SDK or the API.** Triage score 37/100 (below 45, recommended skip). PR rubric 2/10.

## 2. Repo summary [OBSERVED]
- Licence Apache-2.0. Has SECURITY.md (Google VRP) and a CoC. No CONTRIBUTING at the root. External contributions would need the Google CLA.
- Activity: the last merge was 2026-01-06 (a gsutil→gcloud chore). Before that, 2025-03 and 2025-01. 50 PRs are open, mostly a dependabot/renovate backlog. Maintenance is low.
- Structure: Python Dialogflow CX samples (toy webhooks in `dialogflow-cx/webhook/main.py`, a VPC-SC demo with a Flask backend and an OIDC reverse proxy), a Node CX sample, Svelte generative chatbots, CCAI Insights sample data, Terraform.
- Note: `oidc/terraform.tfstate` is committed upstream. Not inspected further.

## 3/4. Threat model and fit
- The webhooks are deliberately minimal demo functions with no auth. In production, Dialogflow CX authenticates them with Google-issued OIDC ID tokens / Service Directory (private network). The VPC-SC demo's real enforcement point is `reverse_proxy_server/app.py: check_user_authentication()`, which uses `google.oauth2.id_token.verify_oauth2_token / verify_firebase_token`.
- AffixIO would add nothing a Google ID token, IAM or mTLS does not already provide at this point. A proof would only attest a yes/no decided by the host. These are official Google samples meant to teach Google Cloud primitives, so a third-party commercial dependency is out of scope for them and very unlikely to be accepted (vendor neutrality 0/2).
- A plausible niche: a PII/consent attestation on Insights exports. There is no enforcement point in this repo, though. That would make a better AffixIO-owned example repo than an upstream PR.

## 5. Integration
None built. Under SPEC §15.4 the fit is not reasonable, so no code was written (H9: minimal and only when justified).

## 6. Tests: baseline, untouched upstream, no GCP credentials, Python 3.13 [OBSERVED]
| Suite | Pass | Fail | Cause |
|---|---|---|---|
| dialogflow-cx/tests (non-integration) | 4 | 3 | "hermetic" tests still open $GOOGLE_APPLICATION_CREDENTIALS (auth_delegator.py:31) |
| dialogflow-cx/tests integration | n/a | 7 deselected | live GCP |
| vpc-sc-demo/backend | 176 | 107 | 104 are a case assertion ('mock_domain.' vs 'MOCK_DOMAIN.'), probably dependency drift with unpinned newer libs. Others need network/ADC |
| vpc-sc-demo reverse proxy | 7 | 3 | DefaultCredentialsError (needs ADC/metadata server) |
| Pinned install (requirements.txt) | fail | — | grpcio==1.57.0 cannot build on Python 3.13 (pkg_resources). Unpinned deps were used instead |
Total: 187 passed, 113 failed. The samples partly work offline. Pins are stale for current Python.
Not run: Node/Svelte lint (no tests there), anything that needs GCP, the AffixIO API (forbidden by the user), the attack suite (no integration to attack).

## 9. Findings for Bert
None new. The SDK was not exercised.

## 11. Fork status
Local branch `affixio-integration` only. Upstream push is DISABLED. No public fork was created (see the final note). Nothing pushed.
## 12. Proofs used: 0. 13. Suspicious content: none found.

## Alternative
If Kris wants a CCAI presence, build an AffixIO-owned demo repo: a Dialogflow CX webhook that issues a signed consent/PII-redaction receipt before data goes to Insights. No upstream contact.
