# FinanceIQ — Guia do Projeto (como funciona)

Este documento explica **como o site funciona**, tanto para desenvolvedores
quanto para IAs como ChatGPT e Claude Code que forem ajudar a evoluir o projeto.

## 1. O que é

Sistema de gestão de finanças pessoais (receitas, despesas, orçamentos,
metas e membros da família), com login de usuário.

## 2. Stack / Tecnologias

- **Node.js** + **Express** — servidor web e API
- **EJS** — templates das páginas (views/)
- **SQLite** (better-sqlite3) — banco de dados em arquivo (financeiq.db)
- **express-session** + store em SQLite (sessions.db) — login por sessão
- **bcryptjs** — hash de senhas
- **helmet** — headers de segurança
- Frontend: HTML/CSS/JS puros em `public/` (sem framework)

## 3. Estrutura de pastas

```
financas-main/
├── server.js            # ponto de entrada: configura Express, sessões, rotas
├── database.js          # cria o banco SQLite e as tabelas
├── middleware/
│   ├── auth.js          # isAuthenticated (API) / isAuthenticatedPage (telas)
│   └── session-store.js # armazena sessões no SQLite
├── routes/
│   ├── auth.js          # /api/auth/login, register, logout, me
│   ├── pages.js         # GET /login e GET / (exige login)
│   ├── transactions.js  # CRUD de transações /api/transactions
│   ├── budgets.js       # /api/budgets
│   ├── goals.js         # /api/goals
│   ├── members.js       # /api/members
│   └── profile.js       # /api/profile
├── views/
│   ├── login.ejs        # tela de login/cadastro
│   └── index.ejs        # dashboard principal
└── public/
    ├── app.js           # JS do dashboard (chama as APIs via fetch)
    ├── login.js         # JS da tela de login
    └── style.css, login.css
```

## 4. Fluxo de autenticação

1. Usuário abre `/login` e faz cadastro ou login (POST `/api/auth/login`).
2. Servidor valida com bcrypt e salva `req.session.user`.
3. Páginas protegidas usam `isAuthenticatedPage` → redireciona para `/login`.
4. APIs usam `isAuthenticated` → retorna 401 JSON.

## 5. Banco de dados (tabelas)

- `users` (id, name, email, password_hash, avatar, created_at)
- `transactions` (id, user_id, type[income|expense], description, amount, date, category, note)
- `budgets` (id, user_id, category, limit) — único por (user_id, category)
- `goals` (id, user_id, name, icon, target, current, deadline)
- `members` (id, user_id, name, relation)

Todas com `FOREIGN KEY ... ON DELETE CASCADE` por usuário.

## 6. Como rodar localmente

```bash
# 1. Instalar dependências
npm install

# 2. Criar o .env (copie de .env.example)
PORT=3000
SESSION_SECRET=uma-string-aleatoria

# 3. Iniciar
node server.js
# ou: npm start
```

Acesse: http://localhost:3000

## 7. Como acessar o site (login)

- Conta existente: email `alexia.plourenco@gmail.com` com a senha cadastrada.
- As configurações ficam em `.env` (porta, secret da sessão, NODE_ENV).
- Para alterar o layout, edite `views/` e `public/style.css`; para novas
  funcionalidades, crie rotas em `routes/` e telas em `views/`.

## 8. Para IAs (ChatGPT / Claude Code) colaborarem

Ao abrir este projeto nesses assistentes, informe:

> "Este é um app Node.js + Express + EJS + SQLite chamado FinanceIQ.
>  O entry point é server.js, o banco está em database.js, as rotas em routes/,
>  as páginas em views/ e o frontend em public/. Rode com `node server.js`
>  e acesse http://localhost:3000. Ajude a evoluir o código seguindo essa
>  estrutura."

E cole o trecho relevante do código (server.js, database.js, etc.) conforme
a dúvida.
