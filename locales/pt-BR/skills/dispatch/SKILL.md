---
name: dispatch
description: >-
  Abre ou atualiza Pull Requests para dev (features/fixes) ou para main
  (releases estáveis), sincroniza tabela/timeline de PRs no README.md e
  CHANGELOG.md, e executa sincronização pós-merge (/dispatch sync) atualizando
  a dev, fechando issues resolvidas e limpando o .session.json.
argument-hint: "[dev | main | sync]"
---

# /dispatch [alvo | sync]

Submete alterações para revisão via Pull Request para `dev` (integração contínua) ou para `main` (release estável), sincroniza documentação, fecha issues automaticamente e higieniza a sessão local.

---

## Algoritmo Determinístico de Decisão

O `/dispatch` define o modo com base estrita na branch atual e nos argumentos (respeitando overrides do `AGENTS.md` do projeto alvo):

1. **Branch efêmera (`feat/*`, `fix/*`, `refactor/*`, `chore/*`)** ➔ **Modo 1: Feature / Fix ➔ `dev`** *(ou `main` em repositórios de branch única)*
2. **Branch de integração (`dev`) sem `sync`** ➔ **Modo 2: Promoção de Release ➔ `main` (SEMPRE)**
3. **Comando explícito `/dispatch sync`** ➔ **Modo 3: Sincronização Pós-Merge & Fechamento de Issues**

> **REGRA INVIOLÁVEL:** Ao rodar `/dispatch` na branch `dev`, NUNCA desvie silenciosamente para o Modo 3. Inicie sempre o Modo 2 (Release para `main`), incorporando eventual sincronização pendente do `README.md` no próprio commit da release.

---

## Modo 1: Feature / Fix ➔ `dev`

1. **Pré-voo com Questionário Anti-Fuga (Read-Only):**
   - Confirme que a branch atual é efêmera (não é `dev` nem `main`).
   - Se a working tree estiver suja (`git status --short`), pause e apresente `ask_question`:
     - `(Recommended) Executar /checkpoint primeiro para auditar e criar os commits atômicos`
     - `Cancelar operação de PR`
   - Se não houver commits à frente de `origin/dev`, pause e apresente `ask_question`:
     - `(Recommended) Desenvolver com /craft e salvar com /checkpoint antes de abrir PR`
     - `Cancelar operação de PR`
   - Verifique se já existe PR aberto: `gh pr list --head <branch> --state open`.

2. **Auditoria de Status de PRs no `README.md`:**
   - Se o `README.md` mantiver tabela de PRs e houver linhas como `🔵 Em Revisão`, verifique no GitHub (`gh pr view <num> --json state -q .state`) e atualize as já mergeadas para `🟣 Merged`.

3. **Confirmação Interativa Obrigatória (`ask_question`):**
   Apresente título, branch destino (`dev`), issue vinculada (`Closes #N`) e atualizações planejadas em `CHANGELOG.md` e `README.md`, aguardando confirmação explícita antes de prosseguir.

4. **Atualização Documental (quando os arquivos existirem no repositório):**
   - Registre a entrega sob `## [Unreleased / Dev]` em `CHANGELOG.md` (e eventual espelho configurado no `AGENTS.md`).
   - Se o `README.md` possuir tabela/timeline Mermaid de PRs recentes, insira o novo PR no topo da tabela (`🔵 Em Revisão`) e no fim da timeline, mantendo **janela deslizante de no máximo 10 PRs recentes**.
   - Commite e envie a atualização documental na branch de trabalho:
     ```bash
     git add README.md CHANGELOG.md
     git commit -m "docs: registra PR de <modulo> no readme e changelog"
     git push origin HEAD
     ```

5. **Abertura do Pull Request:**
   - Preencha `.github/PULL_REQUEST_TEMPLATE.md` (se existir) com `Closes #<issue>` e revisores do `AGENTS.md`.
   - Salve o corpo em arquivo temporário UTF-8 sem BOM e execute:
     ```bash
     gh pr create --base dev --title "<tipo>(<escopo>): <resumo>" --body-file <tempFile>
     ```
   - Retorne a URL do Pull Request criado.

6. **Questionário Pós-Abertura (`ask_question`):**
   - `(Recommended) Aguardar revisão/merge da equipe e executar /dispatch sync`
   - `Retornar para a branch dev para iniciar outro módulo`
   - `Encerrar a sessão de trabalho`

---

## Modo 2: Promoção de Release ➔ `main` (A partir da `dev`)

1. **Pré-voo:**
   - Confirme que a branch atual é `dev` e está sincronizada com `origin/dev`.
   - Garanta a existência de uma Issue de Release no GitHub (ou planeje sua criação).

2. **Confirmação Obrigatória com o Lead (`ask_question`):**
   - Apresente a nova versão SemVer (`vX.Y.Z`), resumo das entregas consolidadas no changelog e promoção `dev` ➔ `main`.

3. **Commit de Preparação da Release na `dev`:**
   - Consolide `## [Unreleased / Dev]` em `## [vX.Y.Z] - YYYY-MM-DD` no `CHANGELOG.md`.
   - Atualize a versão no manifesto do projeto (`package.json`, `pyproject.toml`, `Cargo.toml` etc., se aplicável) e marque módulos concluídos em `docs/REGISTRY.json` como `🟢 prod-active`.
   - Atualize badges/cards/timeline do `README.md` (mantendo a janela de 10 PRs).
   - Commite (`chore(release): prepara versao vX.Y.Z no readme, changelog e manifesto`) e envie para `origin/dev`.

4. **Abertura do PR de Release (`dev` ➔ `main`):**
   - Execute `gh pr create --base main --head dev --title "release(vX.Y.Z): promover dev para producao estavel (main)" --body-file <tempFile>` com `Closes #<issue-release>`.
   - **NUNCA** faça merge direto em `main`; apenas o Lead de Release aprova e faz merge.

---

## Modo 3: Sincronização Pós-Merge & Fechamento de Issues (`/dispatch sync`)

1. **Atualização da `dev` & Limpeza da Branch Efêmera Local:**
   - Faça checkout para `dev` (`git checkout dev`) e atualize (`git pull origin dev`).
   - Remova a branch efêmera local já mergeada (`git branch -d <branch>`).

2. **Fechamento Automático de Issues Resolvidas:**
   - Como o GitHub não fecha automaticamente issues com `Closes #N` ao mergear na `dev`:
     - Liste os últimos PRs mergeados (`gh pr list --state merged --limit 5 --json number,body,title`).
     - Extraia referências `Closes #N` / `Fixes #N`.
     - Para cada issue ainda aberta (`gh issue view <id> --json state -q .state == "OPEN"`), feche imediatamente via CLI:
       ```bash
       gh issue close <id> --comment "Encerrada automaticamente via /dispatch sync após merge do PR na branch dev."
       ```

3. **Sincronização do `README.md`:**
   - Substitua `🔵 Em Revisão` por `🟣 Merged` nos PRs já integrados e confirme o badge da branch `dev`.

4. **Higienização da Sessão (`.session.json`):**
   - Resete `"activeModule": null` em `.session.json`.

5. **Commit e Push (se o `README.md` foi alterado):**
   ```bash
   git add README.md
   git commit -m "docs: sincroniza status de PRs mergeados e fecha issues resolvidas"
   git push origin dev
   ```

6. **Questionário de Encerramento (`ask_question`):**
   - `(Recommended) Iniciar um novo ciclo de desenvolvimento com /scaffold`
   - `Consultar inventário de módulos e serviços com /matrix`
   - `Pausar por agora`

---

## Regra de Ouro: Codificação Segura no Shell (PowerShell & Multiplataforma)
- **NUNCA** passe texto Markdown inline com aspas duplas em `gh pr create --body "..."`.
- **SEMPRE** salve o corpo do PR em arquivo temporário UTF-8 sem BOM e passe via `--body-file <tempFile>`.
