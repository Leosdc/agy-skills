---
name: focus
description: >-
  Define ou consulta o módulo ativo na sessão local do desenvolvedor
  (.session.json, gitignored). Garante isolamento de contexto em projetos solo
  ou equipes com múltiplos desenvolvedores.
argument-hint: "[nome-do-modulo]"
---

# /focus [modulo]

Gerencia o foco de trabalho ativo no arquivo local `.session.json` (gitignored).

---

## Comportamento

### Sem Argumentos (`/focus`)
1. Lê `.session.json` (se não existir, informa que não há sessão inicializada).
2. Verifica o estado do Git (`git branch --show-current`, `git status --short`).
3. Reporta de forma concisa:
   - Módulo ativo (`activeModule`) e especificação vinculada em `specs/` (se houver)
   - Branch atual do Git
   - Arquivos modificados não commitados ou commits à frente da branch base
4. Apresenta `ask_question` orientando o próximo passo (ex.: continuar `/craft`, trocar de módulo ou iniciar `/scaffold`).

### Com Argumento (`/focus <modulo>`)
1. Se `docs/REGISTRY.json` existir, verifica se `<modulo>` está registrado (caso não conste, avisa o desenvolvedor e oferece registrar ou iniciar via `/scaffold`).
2. Atualiza `.session.json`:
   ```json
   {
     "developer": "<usuario-git-ou-atual>",
     "activeModule": "<modulo>",
     "updatedAt": "<timestamp-iso-8601>"
   }
   ```
3. Confirma a troca de contexto, exibe o link clicável para `specs/<modulo>.SPEC.md` (se existir) e apresenta `ask_question`:
   - `(Recommended) Prosseguir para /craft em <modulo>`
   - `Inspecionar detalhes do módulo com /matrix`
   - `Pausar por enquanto`
