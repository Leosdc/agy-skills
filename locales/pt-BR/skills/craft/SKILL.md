---
name: craft
description: >-
  Modo engenheiro sênior interativo focado no módulo ativo em .session.json.
  Implementa seguindo a SPEC canônica, aplica Clean Code, conformidade de
  design system/temas, testes e build automatizados, e conduz o próximo passo
  da esteira via questionário interativo.
argument-hint: "[contexto ou instruções da tarefa]"
---

# /craft [contexto]

Atua como engenheiro sênior de software implementando ou corrigindo o módulo ativo registrado em `.session.json`.

---

## Regras de Operação

### 1. Pré-voo com Questionário Anti-Fuga
- Leia `.session.json`. Se `activeModule` for `null` ou o arquivo não existir, **NÃO codifique às cegas**. Apresente `ask_question`:
  - `(Recommended) Executar /scaffold para criar a especificação canônica e a branch efêmera primeiro`
  - `Executar /focus para selecionar um módulo já registrado`
  - `Continuar em modo livre sem rastreamento de módulo`
- Leia `specs/<activeModule>.SPEC.md` (se existir) e o `AGENTS.md` da raiz do projeto para respeitar todos os contratos e convenções locais.

### 2. Qualidade, Segurança & Padrão Visual
- **Responsabilidade Única (SRP):** Mantenha funções coesas e curtas (heurística de até ~40 linhas).
- **Tratamento Seguro de Erros:** Nunca use `catch` vazio silencioso; proteja workers, I/O e chamadas de rede com timeouts determinísticos e degradação segura (fail-safe).
- **Anti-XSS & Injeção:** Nunca injete dados não confiáveis (usuário, banco ou IA) em sinks HTML brutos (`innerHTML`, `dangerouslySetInnerHTML`) sem sanitização estrita (`DOMPurify.sanitize()`) ou `.textContent`. Parametrize todas as consultas de banco.
- **Tipagem Clara:** Utilize TypeScript, JSDoc ou tipagem nativa da stack nas funções e contratos públicos.
- **Design System & Conformidade de Temas (em tarefas de UI):** Inspecione variáveis CSS, design tokens, temas oficiais (claro, escuro ou customizados) e componentes canônicos do projeto antes de criar novos estilos ou elementos. Nunca use cores hardcoded genéricas nem recrie widgets já existentes.

### 3. Validação Automatizada
Antes de concluir uma rodada de `/craft`, valide suas alterações usando a toolchain real do repositório:
- Execute testes unitários/integração cobrindo a nova lógica e casos de borda.
- Execute linter / typechecker (`tsc --noEmit`, `eslint`, `ruff`, `cargo check`, `go test`, etc., conforme configurado).
- Execute o build de produção (`npm run build` ou equivalente da stack) e, ao alterar servidor/API/CSP, valide respostas e cabeçalhos HTTP reais contra o artefato compilado.
- Atualize o checklist de aceite em `specs/<activeModule>.SPEC.md` refletindo o que foi verificado.
- Mantenha `git status --short` livre de artefatos de build não relacionados à tarefa.

### 4. Condução Interativa Pós-Implementação
Ao concluir a implementação e as validações, resuma alterações e evidências de teste de forma concisa e apresente `ask_question`:
- `(Recommended) Executar /shield para auditoria de segurança e conformidade`
- `Prosseguir diretamente para /checkpoint para salvar o progresso com commits atômicos`
- `Continuar desenvolvendo neste módulo com /craft`
