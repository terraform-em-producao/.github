# Terraform em produção

Repositórios de exemplo do workshop **o mínimo para rodar Terraform de forma segura, rápida e automatizada**.

| Repo | Para que serve | Quem aplica |
|---|---|---|
| [bootstrap](https://github.com/terraform-em-producao/bootstrap) | State remoto (S3 + KMS), zona DNS, roles IAM | Pessoa admin, local, uma vez |
| [atlantis](https://github.com/terraform-em-producao/atlantis) | O runtime: Atlantis no ECS Fargate | GitHub Actions + OIDC |
| [groundwork](https://github.com/terraform-em-producao/groundwork) | Base compartilhada da conta: VPC, logs, guardrails, orçamento | Atlantis |
| [infra-live](https://github.com/terraform-em-producao/infra-live) | Workloads por ambiente (dev/prod) | Atlantis |
| [terraform-modules](https://github.com/terraform-em-producao/terraform-modules) | Módulos da org, versionados por tag | — (consumido) |
| [policies](https://github.com/terraform-em-producao/policies) | Policy as code (OPA/conftest) que o Atlantis roda | — (consumido) |
| [.github](https://github.com/terraform-em-producao/.github) | Workflows reutilizáveis, template de PR | — (consumido) |

```
bootstrap ──► atlantis ──► groundwork ──► infra-live
   (local)    (Actions)    (Atlantis)     (Atlantis)
                  ▲            │              │
              policies    terraform-modules ◄─┘
```

## Os requisitos mínimos

1. **State remoto, criptografado, versionado e com lock** — nunca no laptop, nunca no git.
2. **Nenhuma credencial estática** — OIDC no GitHub Actions, task role no Atlantis.
3. **Menor privilégio por stack** — o Atlantis assume uma role diferente para cada stack.
4. **Tudo pinado** — `required_version`, providers com `.terraform.lock.hcl`, módulos por tag, actions por SHA.
5. **Mudança só por pull request** — plan no PR, apply antes do merge, com aprovação.
6. **Checagem antes do plan** — fmt, validate, tflint, trivy em segundos, sem credencial.
7. **Policy as code** — regras da org avaliadas contra o plan, com override auditável.
8. **States pequenos** — separados por ambiente e por domínio: plan rápido, raio de explosão pequeno.
9. **Detecção de drift** — plan agendado que abre issue quando a realidade diverge do código.
10. **Módulos versionados e testados** — `terraform test` com provider mockado.
