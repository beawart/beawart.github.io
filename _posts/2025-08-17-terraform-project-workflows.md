---
layout: post
title: "Terraform CI/CD Pipeline Best Practices for Azure"
date: 2025-08-17 17:32:00 +1000
categories: [terraform, azure, cicd]
tags: [automation, devops, infrastructure-as-code]
---

When managing cloud infrastructure at scale, a well‑structured CI/CD pipeline for Terraform is essential.  
This guide walks through best practices for building **safe, maintainable, and automated** workflows — with examples geared toward Azure.

---

## 🗂 1. Structure Your Repository for Clarity

A clean repo layout ensures modularity and reusability:

```plaintext
├── modules/
│   ├── networking/
│   ├── compute/
│   └── monitoring/
├── environments/
│   ├── dev/
│   ├── staging/
│   └── prod/
└── .github/workflows/
```

- **Modules** → Encapsulate repeatable resources (networking, compute, alerts)  
- **Environments** → Hold environment‑specific variables and backend configs

---

## 🔐 2. Secure Your State

Store your Terraform state remotely with locking and encryption. For Azure:

```hcl
terraform {
  backend "azurerm" {
    resource_group_name  = "rg-terraform-state"
    storage_account_name = "tfstateprod"
    container_name       = "statefiles"
    key                  = "prod.terraform.tfstate"
  }
}
```

- Enable **state locking** to prevent race conditions  
- Encrypt state files at rest and in transit

---

## 🚦 3. Introduce Manual Approval Gates

In GitHub Actions, approvals can block unintended production changes:

```yaml
jobs:
  deploy-prod:
    needs: plan-prod
    if: github.ref == 'refs/heads/main'
    environment:
      name: production
      url: https://portal.azure.com
```

- Pair this with `terraform plan` artifacts so reviewers can verify before applying

---

## 🧠 4. Detect Changes Before Running Apply

Avoid unnecessary runs by filtering workflow triggers:

```yaml
on:
  push:
    paths:
      - 'environments/prod/**'
      - 'modules/**'
```

This ensures `terraform apply` runs only when relevant code changes.

---

## 📊 5. Monitor Infrastructure with Reusable Modules

Example: Azure Monitor Metric Alerts module:

```hcl
module "cpu_alert" {
  source              = "../../modules/monitoring/metric_alert"
  alert_name          = "HighCPUUsage"
  target_resource_id  = azurerm_linux_virtual_machine.vm.id
  metric_name         = "Percentage CPU"
  threshold           = 80
}
```

- Makes alert creation consistent across environments  
- Reduces duplication in your codebase

---

## 📝 Final Thoughts

A great Terraform CI/CD pipeline balances:
- **Speed** → Fast feedback on changes
- **Safety** → Approvals and controlled rollouts
- **Maintainability** → Modular, reusable components

💡 *Pro Tip:* Test locally with the same backend config as your CI system to catch issues before merge.

---