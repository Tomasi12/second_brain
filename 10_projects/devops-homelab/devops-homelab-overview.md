---
title: DevOps homelab — overview
date: 2026-11-19
tags: [project, career, devops, docker, kubernetes, terraform, aws, jenkins]
status: active
related: [[devops-learning-plan]], [[devops-homelab-pitfalls]]
---

# DevOps homelab — overview

## Summary
I want to change jobs, from "CI/CD & Build Systems Engineer" to **DevOps / Platform Engineer**.
Almost every job ad asks for Docker, Kubernetes, Terraform and a cloud (AWS). This project is
how I learn them: about 16 weeks, 6–8 h per week, with a public GitHub repo `devops-homelab`
as proof for interviews.

## Starting point
- 8 years of Jenkins, Shared Libraries, Artifactory, reproducible builds. That is my main strength.
- EC2 already on my CV.
- Docker, Kubernetes, Helm, Terraform: new. Add them to the CV only after I have used them in the project.

## Goal (definition of done)
- Repo `devops-homelab` on GitHub with a README, a diagram and "start it in 3 commands".
- Jenkins pipeline with a Shared Library: test → build image → Trivy scan → push → Helm deploy.
- Automatic deploy to `dev`, deploy to `prod` after one approval click.
- Infrastructure in Terraform: kind at home, k3s on AWS EC2.
- Updated CV, start applying after Phase 4, at least one certification booked (CKA first).

## Notes
- [[devops-learning-plan]]: the week-by-week plan (Romanian), with the 2026-11-19 review fixes included.
- [[devops-homelab-pitfalls]]: known traps and outdated tools to avoid.

## Constraints
- Time: shares my evenings with SCA ([[overview]] in `static-analyzer/`). If I fall behind,
  never cut Kubernetes; cut the cloud part first.
- Cost: AWS only through Terraform, `terraform destroy` at the end of every session, budget alert on.

## Open questions
- Which certification after CKA: Terraform Associate (cheaper, quick win) or AWS SAA?
- Exam date for CKA (target: weeks 14–18).
