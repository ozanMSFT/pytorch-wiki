# OSS CI SEV

"OSS CI SEV" represents the incident response process for PyTorch OSS CI, including incidents that breaks the [HUD status](https://hud.pytorch.org/build2/pytorch-master), trunk health, PR health, and CI infrastructure stability. The goal of `ci: sev` process is to maintain a healthy trunk for better developer experience.

## Detecting CI SEV

- [OSS] PyTorch Metrics Platform: https://metrics.pytorch.org/
- [FB Only] Green HUD Top Level Metrics: https://fburl.com/unidash/961dprzj 

## Reporting CI SEV

Create an issue that clearly indicates the scope and the impact area. Tag the issue with `ci: sev` label so that it appears on the HUD. https://hud.pytorch.org/build2/pytorch-master

<img src="https://user-images.githubusercontent.com/658840/135177774-bfd3d953-8b60-4261-9183-1b95daaf69c0.png" width="600px" />

## Mitigating CI SEV (Runbook)
- Raise the awareness. SEV events visibility on HUD should be able to help tree-hugger oncalls to clarify if some "test failures" are SEV or infra flaky issues.
- Notify the related tests' owner team. 
- Escalate the issue with `high priority` label if necessary
- After the issue is resolved, simply close the issue (but don't remove the label `ci: sev`).

## Review Meeting

- Gathering the recent SEV issues: https://github.com/pytorch/pytorch/issues?q=is%3Aissue+label%3A%22ci%3A+sev%22+
- Summarize what can we do to prevent similar issues in the future
  - Actionable Items
  - Improved Detection
  