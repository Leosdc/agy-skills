# Diretrizes Operacionais — Spec-Driven Development (`agy-skills`)

Estas diretrizes estabelecem uma esteira determinística, auditável e agnóstica de stack para **Spec-Driven Development**, aplicável a projetos solo ou equipes de engenharia usando **Google Antigravity**.

> **Sobrescritas do Projeto:** Caso o repositório alvo possua um `AGENTS.md` próprio na raiz definindo nomes de branches, revisores, comandos de teste ou padrões visuais específicos, essas declarações têm precedência sobre os padrões abaixo.

---

## Idioma
- Todas as respostas, documentações e especificações devem ser entregues em **Português do Brasil** com acentuação correta (ou no idioma configurado no `AGENTS.md` do projeto alvo).
- Identificadores de código e prefixos de Conventional Commits (`feat`, `fix`, `docs`, `refactor`, `chore`, `test`) seguem convenções técnicas padronizadas.

---

## Estratégia de Branches & Hierarquia de Aprovação (Padrão Configurável)
- **Branch de Integração (`dev`)**: Integração contínua e testes da equipe. Sempre a branch mais atualizada.
- **Branch de Produção (`main`)**: Produção estável e protegida. Push direto é proibido.
- **Aprovador de Release**: Apenas o Lead do Projeto / Responsável de Release (definido no `AGENTS.md` do projeto) aprova e faz merge de `dev` para `main`.
- **Branches Efêmeras**: `feat/<nome>`, `fix/<nome>`, `refactor/<nome>`, `chore/<nome>` nascem a partir de `origin/dev` e são removidas após o merge.
- *(Repositórios com apenas a branch `main` podem sobrescrever a branch de integração para `main` no `AGENTS.md` da raiz.)*

---

## As 8 Skills Operacionais Antigravity
1. `/ignite`: Prepara a máquina e valida o ambiente local (runtimes, dependências, `.env`, `gh CLI`, `.session.json`) de forma idempotente.
2. `/scaffold <tipo>/<nome>`: Cria novo módulo/feature/fix com especificação canônica em `specs/<nome>.SPEC.md`, registro no catálogo e branch efêmera.
3. `/focus [modulo]`: Define ou consulta a frente de trabalho ativa no `.session.json` local.
4. `/craft [contexto]`: Modo engenheiro interativo focado no módulo ativo, seguindo a especificação e validações automáticas.
5. `/shield`: Guardião ativo/passivo de segurança (Segredos, Anti-XSS/Injeção/CSP, AuthN/AuthZ e Privacidade/LGPD).
6. `/checkpoint`: Faz rebase em `origin/dev`, audita segredos, gera plano `.md` em `specs/checkpoints/`, registra journal na Issue do GitHub, cria commits atômicos e envia push.
7. `/dispatch [dev | main | sync]`: Abre Pull Request para `dev` (features/fixes) ou para `main` (releases), sincroniza `README.md` e `CHANGELOG.md`, e no `/dispatch sync` fecha issues resolvidas e limpa a sessão.
8. `/matrix [sync | schema <alvo>]`: Consulta e valida o inventário canônico do projeto (`docs/REGISTRY.json`).

---

## Sequência Canônica de Governança & Questionários Interativos Anti-Fuga

O desenvolvimento segue uma esteira determinística rigorosa:

```text
1. /scaffold ➔ 2. /craft ➔ 3. /shield ➔ 4. /checkpoint ➔ 5. /dispatch ➔ 6. Merge na dev ➔ 7. /dispatch sync
```

### Regras Mandatórias de Condução
1. **Questionários Interativos em Cada Etapa (`ask_question`)**:
   - Toda etapa concluída com sucesso DEVE obrigatoriamente apresentar um questionário interativo ao desenvolvedor, com a primeira opção sendo `(Recommended) <próximo_passo_canônico>`.
2. **Prevenção de Fuga de Sequência**:
   - Se o desenvolvedor tentar executar uma etapa fora de ordem (ex.: `/checkpoint` sem `/shield` em arquivos sensíveis, `/dispatch` com arquivos não commitados ou `/craft` sem módulo ativo no `.session.json`), o agente NÃO deve atuar às cegas nem falhar secamente. Deve interromper e disparar um `ask_question` recomendando a etapa prévia necessária.
3. **Ciclo Completo de Issues (Abertura e Fechamento)**:
   - Como o GitHub não fecha automaticamente issues com `Closes #N` ao mergear em branches não padrão como `dev`, toda issue aberta pelo `/checkpoint` DEVE ser fechada pelo `/dispatch sync` via CLI (`gh issue close <id>`). Nunca deixe issues zumbis abertas.
4. **Higienização de Sessão**:
   - Ao executar `/dispatch sync`, o campo `activeModule` em `.session.json` deve ser resetado para `null`, liberando o ambiente para o próximo ciclo.

---

## Convenções de Commit
Conventional Commits estrito com escopo:
- `feat(auth): adiciona renovacao de token OAuth2 com PKCE`
- `fix(api): corrige condicao de corrida em retentativas de webhook`
- `refactor(ui): desacopla estado de modal do roteador`
- `docs(specs): cria SPEC-001 para webhooks de cobranca`

---

## Padrões de Arquitetura, Qualidade & UI
- **Responsabilidade Única (SRP)**: Mantenha funções coesas e curtas (heurística de até ~40 linhas).
- **Tratamento Explícito de Erros**: Nunca silencie exceções com `catch` vazio; implemente timeouts e degradação segura (fail-safe).
- **Contratos Tipados**: Documente APIs públicas e modelos de dados com TypeScript, JSDoc ou tipagem nativa da linguagem.
- **Design System & Conformidade com Temas**: Sempre que o projeto definir componentes canônicos, variáveis CSS ou múltiplos temas (claro/escuro/customizados), inspecione e reutilize os padrões oficiais sem usar cores hardcoded genéricas.
- **Validação Automatizada**: Execute testes, linter, typecheck e build antes de encerrar o `/craft`.

---

## Leis Inegociáveis de Segurança & Privacidade
1. **Blindagem de Segredos**: Nunca insira chaves de API, senhas ou tokens no código commitado. Mantenha `.env*` e `.session.json` no `.gitignore`.
2. **Prevenção contra XSS & Injeção**: Nunca escreva dados de usuários, banco ou IA em sinks HTML brutos (`innerHTML`, `dangerouslySetInnerHTML`) sem sanitização estrita (`DOMPurify.sanitize()`) ou `.textContent`. Use queries parametrizadas.
3. **Controle de Acesso (AuthN / AuthZ)**: Valide autenticação e posse/RBAC no servidor ou nas regras do banco em toda operação protegida.
4. **Minimização de Dados (LGPD / GDPR)**: Colete e exponha apenas o estritamente necessário; nunca vaze UIDs internos, tokens ou dados sensíveis em logs públicos ou DOM.
