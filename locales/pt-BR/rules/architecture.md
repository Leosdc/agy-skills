# Arquitetura e Padrões de Código

1. **Modularidade e Fronteiras Claras**:
   - Organize funcionalidades em módulos, pacotes ou serviços bem delimitados seguindo as convenções do repositório registradas em `docs/REGISTRY.json` e no `AGENTS.md` da raiz.
   - Evite acoplamento direto entre domínios não relacionados; comunique-se por contratos explícitos, interfaces ou barramentos de eventos.

2. **Qualidade e Clean Code**:
   - **Responsabilidade Única (SRP)**: Mantenha funções pequenas e coesas (heurística de até ~40 linhas).
   - **Clareza**: Nomes descritivos e sem abreviações obscuras.
   - **Tratamento Explícito de Erros**: Trate falhas de rede, I/O e execução assíncrona com logs claros e fallback seguro — nunca silencie erros com `catch` vazio.
   - **Contratos Tipados**: Utilize TypeScript, JSDoc ou a tipagem nativa da stack em todas as interfaces e funções críticas.

3. **UI, Temas & Componentes Canônicos** *(quando aplicável)*:
   - Inspecione variáveis CSS, design tokens e componentes compartilhados antes de criar novos controles visuais.
   - Verifique contraste e layout em todos os temas oficiais do projeto (claro, escuro ou customizados) e em diferentes resoluções.

4. **Validação Automática**:
   - Antes de finalizar o `/craft` ou abrir PR, execute as validações configuradas no projeto (testes unitários/integração, linter, typecheck e build de produção).
