---
title: DevOps homelab — pitfalls and outdated tools
date: 2026-11-19
tags: [devops, kubernetes, terraform, aws, pitfalls]
status: active
related: [[devops-homelab-overview]], [[devops-learning-plan]]
---

# DevOps homelab — pitfalls and outdated tools

## Summary
Things found when reviewing the first version of the plan (2026-11-19): tools that are no
longer the right choice, and traps that usually block people. Check them again before each phase,
because this field changes fast.

## Outdated, replaced in the plan
| Old choice | Why not | Use instead |
|---|---|---|
| ingress-nginx | Retirement announced Nov 2025, maintenance ended Mar 2026 | **Gateway API** (Envoy Gateway, NGINX Gateway Fabric or Traefik). Also in the CKA curriculum. |
| Bitnami PostgreSQL chart | Bitnami stopped free public images/charts in 2025 | **CloudNativePG** operator (also teaches CRDs/operators) or my own chart |
| S3 backend + DynamoDB lock | DynamoDB locking is deprecated | S3 native locking: `use_lockfile = true` |
| "AWS free tier 12 months" | Changed July 2025 to a credit-based free plan for new accounts | Check the current offer on the AWS page before creating the account |

## Traps
- **RAM on 16 GB:** kind (3 nodes) + Jenkins + registry + Postgres is tight. Set a memory limit in
  `%UserProfile%\.wslconfig`. Daily: 1 control-plane + 1 worker; 3 nodes only for scheduling exercises.
- **Jenkins container → kind API:** kind listens on `127.0.0.1`, unreachable from another container.
  Fix: `docker network connect kind <jenkins-agent>` and use `kind get kubeconfig --internal`.
- **kind + local registry:** `kind load docker-image` is fine for Phase 3. For the pipeline (Phase 4),
  configure a containerd registry mirror in the kind cluster config.
- **Docker socket in the Jenkins agent:** works, but it is root on the host. Know why it is risky;
  mention Kaniko / BuildKit rootless as the alternative at interviews.
- **kind does not teach the CKA:** the exam uses kubeadm (install, upgrade, etcd backup/restore,
  `systemctl`, `journalctl`). Practise on 2–3 VMs (Multipass/Vagrant) or in KodeKloud labs.
- **CKA curriculum also includes** Kustomize, Helm, CRDs/operators, Gateway API.
- **EC2 size for k3s:** t3.micro (1 GB) is too small; use t3.small or bigger.
- **Public IPv4 costs money** (about $0.005/h per address), even on free plans.
- **ECR tokens expire after 12 h.** k3s `registries.yaml` does not refresh them by itself, even with
  an IAM instance role: add a cron job / credential helper that refreshes the token.

## Money tips
- **CKA voucher:** Linux Foundation discounts are usually 40–50% around Black Friday / Cyber Monday.
  A voucher is valid 12 months and includes 2 killer.sh simulator sessions (use them in the last week).
