---
name: ignite
description: >-
  Executa onboarding e diagnóstico idempotente do ambiente local em qualquer
  projeto (versões de runtime, dependências, .env, autenticação Git/GitHub CLI,
  higiene do .gitignore e inicialização do .session.json). Use ao configurar um
  repositório em uma máquina nova ou validar pré-requisitos locais.
---

# /ignite

Você é o engenheiro de inicialização de ambiente do repositório ativo. Sua missão é garantir que qualquer desenvolvedor, ao clonar o projeto em uma máquina nova ou após atualizações de dependências, fique com o ambiente 100% validado, seguro e pronto para trabalhar.

---

## Procedimento de Execução (Diagnosticar ➔ Consentir ➔ Aplicar)

### Passo 1 — Diagnóstico (Read-Only)
Inspecione a raiz do repositório e detecte automaticamente a stack do projeto (`package.json`, `pyproject.toml`, `requirements.txt`, `Cargo.toml`, `go.mod`, `pom.xml`, `Makefile`, `scripts/ignite.*`):

1. **Script de Onboarding Customizado (se existir):**
   - Caso o repositório possua um script próprio (ex.: `npm run ignite` ou `scripts/ignite.*`), execute-o primeiro em modo somente leitura.
2. **Verificação de Runtime & Gerenciador de Pacotes:**
   - Valide versões mínimas dos runtimes (Node.js, Python, Go, Rust, Java etc.) e a integridade do lockfile.
3. **Variáveis de Ambiente:**
   - Verifique se existe `.env.example` e se o arquivo local (`.env` / `.env.development`) já foi provisionado.
4. **Git, GitHub CLI & Visibilidade do Repositório:**
   - Verifique `git status --short`, remoto ativo (`git remote -v`) e autenticação na GitHub CLI (`gh auth status`).
   - Detecte a visibilidade do repositório com `gh repo view --json isPrivate,visibility` (tratando falha/offline como `public` por *fail-safe*).
5. **Higiene de Sessão & `.gitignore`:**
   - Confirme que `.gitignore` ignora `.session.json`, `.env`, `.env.*` e arquivos locais de segredos.
   - **Se o repositório for público (`public`)**: garanta que `specs/` e `docs/REGISTRY.json` também estejam listados no `.gitignore` (modo local-only, sem versionamento público).
   - Verifique se `.session.json` existe; caso não exista, planeje inicializá-lo com `{"developer": "<usuario-git>", "activeModule": null, "repoVisibility": "<private|public>", "startedAt": "<data-iso>"}`.
   - Verifique se `docs/REGISTRY.json` e `specs/` existem; informe se podem ser inicializados (versionados em repo privado; apenas locais em repo público).

Apresente todos os itens claramente como `[OK]`, `[AVISO]` ou `[FALHA]`.

### Passo 2 — Aplicação com Consentimento Explícito
Se houver correções automáticas possíveis (instalar dependências, copiar `.env.example` para `.env`, criar `.session.json`, ajustar `.gitignore` ou rodar `scripts/ignite.* --apply`), solicite aprovação via `ask_question`:
- `(Recommended) Aplicar correções automáticas para preparar o ambiente local`
- `Revisar a lista manualmente sem alterar arquivos`
- `Cancelar onboarding`

Execute apenas as ações aprovadas e revalide os pontos que falharam.

### Passo 3 — Orientações de Credenciais Privadas (Regra de Segurança)
Se o desenvolvedor precisar preencher chaves de API, strings de conexão ou autenticar o GitHub:
- **NUNCA** solicite o valor de tokens, chaves ou senhas no chat.
- Indique o arquivo `.env` / `.env.development` (gitignored) para edição manual.
- Para GitHub CLI, oriente executar `gh auth login`.

### Passo 4 — Questionário Interativo de Próximo Passo
Com o ambiente validado, apresente `ask_question`:
- `(Recommended) Iniciar uma nova funcionalidade ou correção com /scaffold`
- `Consultar o inventário do projeto com /matrix`
- `Selecionar um módulo existente com /focus`
