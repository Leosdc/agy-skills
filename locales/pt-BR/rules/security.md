---
trigger: always_on
description: >-
  Regras inegociáveis de segurança e privacidade cobrindo blindagem de segredos,
  prevenção contra XSS/injeção, autenticação/autorização e conformidade LGPD.
---

# Regras Obrigatórias de Segurança & Privacidade (LGPD / GDPR)

1. **Blindagem de Segredos e Credenciais**:
   - NUNCA insira chaves de API, senhas, chaves privadas, credenciais de serviço ou tokens diretamente no código ou documentação.
   - Segredos privados devem residir exclusivamente no backend/servidor; nunca os exponha em bundles de cliente ou variáveis públicas (`VITE_*`, `NEXT_PUBLIC_*`, `REACT_APP_*`).
   - Garanta que `.env`, `.env.local`, `.env.development` e `.session.json` estejam sempre listados no `.gitignore`.

2. **Prevenção contra XSS & Injeção**:
   - NUNCA escreva dados de usuários, banco ou respostas de IA diretamente em sinks HTML (`innerHTML`, `outerHTML`, `document.write`, `dangerouslySetInnerHTML`) sem sanitização estrita (`DOMPurify.sanitize()`) ou `.textContent`.
   - Utilize sempre queries parametrizadas / prepared statements / ORMs validados para impedir SQL/NoSQL injection.
   - Aplique cabeçalhos de segurança com princípio do menor privilégio (CSP, CORS, rate limiting) nos servidores HTTP.

3. **Autenticação & Autorização (AuthN / AuthZ)**:
   - Exija autenticação ativa para rotas protegidas, APIs e regras de banco de dados.
   - Valide posse do recurso (`userId == auth.uid`) ou permissões RBAC no lado do servidor em toda leitura/escrita sensível.

4. **Minimização de Dados & LGPD**:
   - Colete, armazene e trafegue apenas os dados estritamente necessários à operação.
   - Nunca exponha identificadores confidenciais (UIDs internos, tokens, e-mails privados, IDs de sessão) em telas públicas, logs do navegador ou telemetria externa.

5. **Proteção de Artefatos Internos em Repositórios Públicos**:
   - Em repositórios públicos (`gh repo view --json isPrivate` == `false` ou indeterminado), NUNCA versione nem envie `specs/`, `specs/checkpoints/` ou `docs/REGISTRY.json` ao Git remoto (salvo opt-in explícito no `AGENTS.md` do projeto).
   - Mantenha esses artefatos exclusivamente locais, listados no `.gitignore`, evitando exposição pública de arquitetura interna, planejamento e planos de checkpoint.
