---
name: matrix
description: >-
  Consulta, valida e sincroniza o inventário canônico do projeto em
  docs/REGISTRY.json (módulos, serviços, pacotes, schemas e especificações).
  Use para explorar a arquitetura, verificar status de entregas ou detectar
  código não registrado.
argument-hint: "[sync | schema <dominio-ou-entidade>]"
---

# /matrix [comando]

Consulta e mantém o arquivo `docs/REGISTRY.json` — a *Source of Truth* estruturada de módulos, serviços, pacotes, schemas de dados e especificações do projeto.

---

## Comandos

### Sem Argumentos (`/matrix`)
1. Lê `docs/REGISTRY.json` (caso não exista, oferece inicializá-lo a partir do template padrão).
2. Exibe uma tabela organizada por domínio (`modules`, `services`, `packages`, `data` etc.) contendo:
   - ID & Nome
   - Status operacional (`🟢 prod-active`, `🟡 in-progress`, `🟣 dev`, `🔴 deprecated`)
   - Ponto de entrada (link clicável)
   - Especificação vinculada (`specs/<id>.SPEC.md`, link clicável)

### `/matrix sync`
1. Varre os diretórios declarados no campo `path` de cada domínio em `docs/REGISTRY.json` e todos os arquivos em `specs/*.SPEC.md`.
2. Reporta divergências:
   - Módulos ou serviços existentes no código mas ausentes do `docs/REGISTRY.json`
   - Entradas no catálogo apontando para arquivos ou specs inexistentes
   - Especificações órfãs sem registro no catálogo
3. Pergunta via `ask_question` se deve reconciliar e atualizar `docs/REGISTRY.json`.

### `/matrix schema <alvo>`
1. Inspeciona o domínio de dados em `docs/REGISTRY.json` e os arquivos de contrato/schema do projeto (Prisma, migrações SQL, Zod, JSON Schema, regras/índices Firestore, Protobuf, OpenAPI etc.).
2. Resume campos, restrições, índices e fronteiras de autorização de `<alvo>`.
