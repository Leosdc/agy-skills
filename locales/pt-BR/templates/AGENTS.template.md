# <Nome do Projeto> — Diretrizes Operacionais para Agentes Antigravity

> Copie este template para a raiz do seu repositório como `AGENTS.md` e substitua os campos entre `<...>`.

## Equipe & Responsabilidades
- **Lead do Projeto / Aprovador de Produção:** `<Nome do Lead>` (`@handle-lead`)
- **Engenheiros / Revisores:** `<Dev 2>` (`@dev2`), `<Dev 3>` (`@dev3`) *(ou desenvolvedor solo)*

## Idioma
- Todas as respostas, documentações e especificações devem ser entregues em **Português do Brasil** com acentuação correta.
- Identificadores de código e commits seguem padrões técnicos usuais (Conventional Commits).

## Stack Técnica & Comandos de Validação
- **Stack:** `<ex.: TypeScript, React, Node.js, PostgreSQL>`
- **Instalação:** `<ex.: npm ci>`
- **Desenvolvimento:** `<ex.: npm run dev>`
- **Testes:** `<ex.: npm test>`
- **Lint / Typecheck:** `<ex.: npm run lint && npm run typecheck>`
- **Build:** `<ex.: npm run build>`

## Estratégia de Branches & Governança
- **`dev`**: Branch de integração contínua (sempre a mais atualizada). *(Altere para `main` se o projeto usar apenas uma branch permanente.)*
- **`main`**: Produção estável e protegida.
- **Aprovador de Produção**: Apenas `@handle-lead` aprova e faz merge de releases de `dev` para `main`.
- **Branches Efêmeras**: `feat/<nome>`, `fix/<nome>`, `refactor/<nome>` nascem de `origin/dev` e são deletadas após o merge.

## Esteira Spec-Driven Development (`agy-skills`)
`1. /scaffold` ➔ `2. /craft` ➔ `3. /shield` ➔ `4. /checkpoint` ➔ `5. /dispatch` ➔ `6. Merge na dev` ➔ `7. /dispatch sync`

- Todas as especificações ficam em `specs/<nome>.SPEC.md`.
- Todos os módulos/serviços ficam catalogados em `docs/REGISTRY.json`.
- A sessão ativa do desenvolvedor fica em `.session.json` (gitignored).

## Padrão Visual & Design System *(opcional — remova em projetos sem UI)*
- **Temas Oficiais:** `<ex.: claro, escuro>` usando variáveis CSS em `<caminho/para/themes.css>`.
- **Componentes Canônicos:** Reutilize componentes oficiais em `<caminho/para/components>` sem cores hardcoded genéricas.
