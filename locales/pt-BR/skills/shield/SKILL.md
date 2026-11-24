---
name: shield
description: >-
  Guardião ativo e passivo de auditoria de segurança, privacidade e
  conformidade. Inspeciona diffs em busca de vazamento de segredos, falhas de
  XSS/injeção, quebras de autenticação/autorização, regressões de CSP/headers e
  violações de minimização de dados (LGPD/GDPR) antes de commitar ou abrir PR.
---

# /shield — Guardião de Segurança & Conformidade

Aplica as leis inegociáveis de segurança e privacidade sobre toda alteração de código. Ative automaticamente sempre que manipular APIs, autenticação, cabeçalhos HTTP/CSP, regras de banco ou dados pessoais, e explicitamente entre `/craft` e `/checkpoint`.

---

## Checklist de Auditoria (As 4 Leis + Hardening)

Inspecione `git status --short` e `git diff` em todos os arquivos modificados e novos:

1. **Lei 1 — Blindagem de Segredos e Credenciais:**
   - Varra o diff procurando chaves de API, senhas, chaves privadas, JWTs, segredos OAuth ou strings de conexão hardcoded.
   - Confirme que segredos privados de backend NUNCA vazam para bundles cliente ou prefixos públicos (`VITE_*`, `NEXT_PUBLIC_*`, `REACT_APP_*`).
   - Valide via `git check-ignore` que `.session.json`, `.env` e `.env.*` (exceto `.env.example`) estão ignorados no `.gitignore`.
   - **Em caso de violação:** Rejeição imediata; bloqueie o commit até a correção.

2. **Lei 2 — Sanitização de Entradas, Anti-XSS & Injeção:**
   - Verifique se nenhum dado externo (formulários, query params, banco ou respostas de IA) chega a sinks HTML brutos (`innerHTML`, `outerHTML`, `document.write`, `dangerouslySetInnerHTML`) sem `DOMPurify.sanitize()` ou `.textContent`.
   - Verifique se consultas SQL/NoSQL e comandos de shell são parametrizados e livres de injeção.
   - Ao alterar cabeçalhos HTTP ou CSP, confirme princípio do menor privilégio (evitar `'unsafe-eval'`, não abrir `script-src` indevidamente, restringir `worker-src`/`connect-src`).

3. **Lei 3 — Autenticação & Autorização (AuthN / AuthZ):**
   - Verifique se rotas protegidas, server actions e regras de banco exigem autenticação ativa.
   - Confirme validação de posse (`userId == auth.uid`) ou RBAC no servidor em todas as operações protegidas.

4. **Lei 4 — Privacidade & Minimização de Dados (LGPD / GDPR):**
   - Confirme que apenas dados estritamente necessários são coletados, armazenados ou registrados em log.
   - Garanta que UIDs internos, tokens de sessão, e-mails privados e payloads sensíveis nunca sejam expostos no DOM público, logs do cliente ou telemetria.

---

## Relatório & Condução Interativa Pós-Auditoria
Emita uma tabela objetiva com cada Lei (✅ Aprovado / ⚠️ Atenção / ❌ Reprovado + evidência concreta) e o veredito final.

Em seguida, apresente obrigatoriamente `ask_question`:
- `(Recommended) Prosseguir para /checkpoint para gerar o plano .md e salvar as alterações` *(quando aprovado)*
- `Retornar para /craft para corrigir achados ou refatorar código`
- `Pausar por enquanto`
