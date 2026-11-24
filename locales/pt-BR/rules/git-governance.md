---
trigger: model_decision
description: >-
  Aplicar ao criar branches, executar rebase, escrever Conventional Commits,
  abrir Pull Requests, gerenciar releases ou usar a GitHub CLI (gh).
---

# Governança Git & GitHub Flow

1. **Duas Branches Permanentes (Padrão Configurável)**:
   - `dev`: Branch de integração contínua. Sempre a mais atualizada, onde features e correções convergem.
   - `main`: Branch de produção estável e protegida contra push direto.
   - *(Projetos que utilizam apenas `main` podem sobrescrever essa regra no `AGENTS.md` da raiz.)*

2. **Hierarquia de Aprovação em Produção**:
   - Toda alteração entra primeiro na `dev` via Pull Request.
   - Apenas o Lead do Projeto / Responsável de Release aprova e faz merge de `dev` para `main`.
   - O agente Antigravity NUNCA deve fazer push ou merge direto em `main`.

3. **Ciclo de Vida de Branches Efêmeras**:
   - Branches de trabalho (`feat/<nome>`, `fix/<nome>`, `refactor/<nome>`, `chore/<nome>`) nascem sempre de `origin/dev` e são removidas após o merge.
   - Execute rebase contra `origin/dev` antes de abrir ou atualizar PRs para preservar histórico limpo e linear.

4. **Conventional Commits**:
   - Formato obrigatório: `<tipo>(<escopo>): <descrição concisa>`.
   - Exemplos: `feat(cobranca): adiciona validacao de assinatura de webhook`, `fix(ui): corrige travamento de scroll no modal`.

5. **Codificação Segura no Shell para GitHub CLI (`gh`)**:
   - NUNCA passe texto Markdown com crases (`` ` ``) inline dentro de aspas duplas no PowerShell.
   - Salve sempre corpos de issues, comentários e PRs em arquivo temporário UTF-8 sem BOM e passe via `--body-file <caminho>`.
