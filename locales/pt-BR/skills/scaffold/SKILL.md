---
name: scaffold
description: >-
  Cria toda a estrutura de Spec-Driven Development para um novo módulo,
  feature, serviço ou correção em qualquer projeto. Gera especificação canônica
  em specs/<nome>.SPEC.md, registra em docs/REGISTRY.json, cria branch efêmera
  a partir de origin/dev (ou base configurada) e foca o .session.json.
argument-hint: <tipo>/<nome> [briefing]
---

# /scaffold <tipo>/<nome> [briefing]

Cria a estrutura inicial de uma nova funcionalidade, módulo, serviço, componente ou correção seguindo **Spec-Driven Development** com governança **Plan-Validate-Execute**.

Tipos aceitos (adapte à arquitetura declarada no `AGENTS.md` ou `docs/REGISTRY.json` do projeto alvo):
- `feat` / `module` / `feature`: Novo módulo ou funcionalidade voltada ao usuário (branch `feat/<nome>`)
- `fix`: Correção de bug ou endurecimento técnico (branch `fix/<nome>`)
- `service` / `backend` / `api`: Nova rota, serviço de backend ou worker (branch `feat/<nome>`)
- `ui` / `component`: Nova tela ou componente visual (branch `feat/<nome>`)
- `refactor` / `chore`: Refatoração estrutural ou tooling (branch `refactor/<nome>` ou `chore/<nome>`)

*(Caso o usuário omita `<tipo>/`, ex.: `/scaffold fix-login-timeout` ou `/scaffold billing-webhooks`, infira o prefixo da branch como `fix/<slug>` ou `feat/<slug>`.)*

---

## Regra Mandatória de Localização do Spec
Todo e qualquer documento de especificação gerado **DEVE OBRIGATORIAMENTE** ser salvo no diretório canônico:
👉 **`specs/<nome>.SPEC.md`**

> **ATENÇÃO:** NUNCA salve arquivos `SPEC.md` soltos na raiz do projeto ou isolados em subpastas sem que a especificação principal esteja em `specs/`. A pasta `specs/` é o inventário unificado e *Source of Truth* das especificações de engenharia.
> **Repositórios Públicos:** Verifique `gh repo view --json isPrivate,visibility` (ou `repoVisibility` em `.session.json`). Se o repositório for **público** (ou visibilidade indeterminada), `specs/` e `docs/REGISTRY.json` devem ser adicionados ao `.gitignore` e mantidos **exclusivamente locais** (nunca commitados no Git remoto, salvo opt-in explícito no `AGENTS.md` da raiz).

---

## Padrão Plan-Validate-Execute

### 1. Plan (Read-Only)
1. Extraia `<tipo>`, `<nome>` e eventual `[briefing]` dos argumentos.
2. Identifique a branch base de integração no `AGENTS.md` da raiz (padrão: `origin/dev`; fallback para `origin/main` caso o repositório não utilize branch `dev`).
3. **Pré-voo Anti-Fuga (Verificação de Sessão Aberta):**
   - Verifique `.session.json`. Se já existir um `activeModule` ativo não finalizado, pause e apresente `ask_question`:
     - `(Recommended) Executar /dispatch sync para sincronizar e fechar o módulo anterior`
     - `Sobrescrever sessão ativa e iniciar novo /scaffold`
     - `Cancelar operação`
4. Consulte `docs/REGISTRY.json` (se existir) para garantir que `<nome>` ainda não existe e descubra o próximo número sequencial `SPEC-XXX` em `specs/*.SPEC.md`.
5. Verifique o estado do Git (`git fetch origin`, `git status --short`).
6. Elabore o plano conciso contendo:
   - **Especificação canônica:** `specs/<nome>.SPEC.md` (`SPEC-XXX`)
   - **Arquivos alvo previstos para o `/craft`:** caminhos a criar ou alterar
   - **Atualização no catálogo:** registro em `docs/REGISTRY.json` com `"spec": "specs/<nome>.SPEC.md"` (inicializando `docs/REGISTRY.json` com schema mínimo caso o projeto ainda não possua)
   - **Branch efêmera:** `<prefixo>/<nome>` a partir da `<branch-base>`
   - **Foco da sessão:** encadeamento automático de `/focus <nome>` no `.session.json`

### 2. Validate
Apresente o plano e solicite confirmação explícita do desenvolvedor via `ask_question` antes de criar arquivos ou branches:
- `(Recommended) Confirmar e executar /scaffold <nome>`
- `Ajustar escopo, nome do spec ou nome da branch antes de criar`
- `Cancelar operação`

### 3. Execute
1. **Crie e faça checkout na branch efêmera:**
   ```bash
   git checkout -b <prefixo>/<nome> <branch-base>
   ```
2. **Crie a especificação canônica em `specs/<nome>.SPEC.md`** contendo:
   - Metadados formais (Status `🟡 in-progress`, Tipo, Arquivos/Módulos Afetados, Data, Autor).
   - `## 1. Visão Executiva & Contexto`
   - `## 2. Requisitos Funcionais & Não-Funcionais`
   - `## 3. Arquitetura da Solução & Contratos` (APIs, schemas, eventos, fluxo / diagrama Mermaid)
   - `## 4. Segurança, Sanitização & Privacidade (LGPD / GDPR)`
   - `## 5. Critérios de Aceite & Roteiro de Testes`
3. **Crie o boilerplate inicial** (apenas para módulos/arquivos novos, com tratamento fail-safe e tipagem limpa; pule a criação de boilerplate em correções pontuais sobre arquivos existentes).
4. **Registre a entrada em `docs/REGISTRY.json`** com `"status": "🟡 in-progress"` e `"spec": "specs/<nome>.SPEC.md"`.
5. **Encadeie automaticamente `/focus <nome>`** atualizando `.session.json`.
6. **Questionário Interativo de Próximo Passo (`ask_question`):**
   - `(Recommended) Prosseguir para /craft para iniciar a implementação da especificação`
   - `Revisar e editar a especificação canônica criada em specs/`
   - `Pausar por enquanto`
