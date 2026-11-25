---
name: checkpoint
description: >-
  Fecha um ciclo de trabalho com segurança em uma branch efêmera. Faz pré-voo
  anti-secrets, rebase contra origin/dev, gera plano formal em Markdown em
  specs/checkpoints/, registra journal técnico em Issue no GitHub, cria
  Conventional Commits atômicos, envia push e conduz para /dispatch.
---

# /checkpoint

Fecha com segurança o progresso no módulo ativo seguindo rigorosamente o padrão **Plan-Validate-Execute**.

---

## Procedimento

### 1. Plan (Read-Only & Geração do Plano em Markdown)
1. Identifique `activeModule` e `developer` em `.session.json`.
2. Verifique o estado do Git (`git status --short`, `git diff --stat`, branch atual).
3. **Pré-voo Anti-Fuga (Auditoria de Segurança & Visibilidade):**
   - Varra arquivos modificados e novos em busca de segredos acidentais (`API_KEY`, chaves privadas, tokens, senhas). Se encontrar, **ABORTE IMEDIATAMENTE**.
   - Verifique a visibilidade do repositório (`gh repo view --json isPrivate,visibility` ou `.session.json`). Se for **público** (ou indeterminado, salvo opt-in no `AGENTS.md`), confirme que `specs/`, `specs/checkpoints/` e `docs/REGISTRY.json` estão no `.gitignore` e **EXCLUA-OS** dos commits atômicos (mantendo o plano `.PLAN.md` apenas localmente).
   - Se houver alterações em arquivos sensíveis (auth, servidor/API, CSP/headers, regras de banco) e `/shield` ainda não tiver sido executado nesta sessão, pause e apresente `ask_question`:
     - `(Recommended) Executar /shield primeiro para auditoria estrita de segurança e conformidade`
     - `Prosseguir diretamente com o plano do /checkpoint`
     - `Cancelar o checkpoint`
4. Inspecione o `README.md` (caso possua tabela de PRs) e verifique se há PRs marcados como `🔵 Em Revisão` / `🔵 In Review` já mergeados no GitHub (`gh pr view`), propondo sincronização para `🟣 Merged`.
5. Verifique no GitHub se existe uma Issue aberta com a etiqueta `modulo:<activeModule>` (ou `module:<activeModule>`). Caso não exista, planeje criar a etiqueta e abrir a Issue.
6. **Geração Obrigatória do Plano em Markdown:**
   - Crie `specs/checkpoints/YYYYMMDD_<activeModule>.PLAN.md` contendo:
     - **Metadados:** Data, Desenvolvedor, Módulo Ativo (`SPEC-XXX`), Branch Atual.
     - **Status de Arquivos:** Tabela com todos os arquivos modificados/novos e seu escopo.
     - **Auditoria Anti-Secrets & `/shield`:** Confirmação explícita de `0` vazamentos.
     - **Sincronização de PRs & README:** Status verificado.
     - **Issue & Journal no GitHub:** Número da Issue (ou criação prevista) e corpo exato do comentário técnico.
     - **Commits Atômicos Propostos:** Mensagens no padrão Conventional Commits agrupando os arquivos de cada fatia.
     - **Operações de Git:** Sequência exata de rebase, commit e push.
7. Apresente o link clicável para `specs/checkpoints/YYYYMMDD_<activeModule>.PLAN.md`.

### 2. Validate
Solicite aprovação explícita do desenvolvedor via `ask_question`:
- `(Recommended) Aprovar e executar o plano specs/checkpoints/YYYYMMDD_<activeModule>.PLAN.md`
- `Modificar o plano antes de prosseguir`
- `Cancelar o checkpoint`

### 3. Execute
1. Execute rebase limpo contra a branch de integração (padrão `origin/dev`, ou base configurada no `AGENTS.md`):
   ```bash
   git fetch origin
   git rebase --autostash origin/dev
   ```
2. Crie a Issue no GitHub (se ainda não existir) e publique o comentário técnico com `gh issue comment` via `--body-file` (UTF-8 sem BOM).
3. Crie os commits atômicos aprovados (incluindo `specs/checkpoints/YYYYMMDD_<activeModule>.PLAN.md` na fatia documental **somente em repositórios privados**; em repositórios públicos, mantenha `specs/` e `docs/REGISTRY.json` exclusivamente locais no `.gitignore`).
4. Envie a branch para o remoto:
   ```bash
   git push -u origin HEAD
   ```
5. **Questionário Interativo Obrigatório de Condução (`ask_question`):**
   - `(Recommended) Prosseguir para /dispatch para abrir o Pull Request na dev`
   - `Continuar desenvolvendo no módulo ativo antes de abrir o PR`
   - `Encerrar a sessão de trabalho por enquanto`

---

## Regra de Ouro: Codificação Segura no Shell (PowerShell & Multiplataforma)
No PowerShell, a crase (`` ` ``) é o caractere de escape da linguagem! Strings Markdown com crases dentro de aspas duplas corrompem sequências como `` `v ``, `` `f ``, `` `n `` e `` `t `` no GitHub.

**Protocolo Obrigatório:**
- **NUNCA** passe Markdown inline com aspas duplas para o `gh`.
- **SEMPRE** salve corpos de issues e comentários em arquivo temporário UTF-8 sem BOM e passe via `--body-file <caminho>`.
- Em scripts PowerShell, use exclusivamente single-quoted here-strings (`@' ... '@`) ao montar literais Markdown.
