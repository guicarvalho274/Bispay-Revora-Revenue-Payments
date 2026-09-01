## `POST /auth/google` — Autenticação com Google

Autentica ou cria conta de usuário utilizando o Google Identity Services. O frontend envia o ID Token (JWT) obtido pelo botão "Sign in with Google".

### Body

```json
{
  "credential": "eyJhbGciOiJSUzI1NiIs..."
}
```

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| credential | string | sim | ID Token JWT retornado pelo Google |

### Validações (Zod)

| Campo | Regra | Erro |
|---|---|---|
| credential | obrigatório, string | `credential_invalid` |

### Fluxo Interno — 3 Cenários

```
1. Valida payload com Zod
2. Verifica JWT do Google com google-auth-library
3. Busca usuário por email + provider GOOGLE

   ┌─────────────────────────────────────────────────────────┐
   │ CENÁRIO 1: Usuário existe + provider GOOGLE existe     │
   │ → Login direto                                         │
   └─────────────────────────────────────────────────────────┘

   ┌─────────────────────────────────────────────────────────┐
   │ CENÁRIO 2: Usuário existe + provider GOOGLE não existe │
   │ → Vincula provider GOOGLE ao usuário existente         │
   │ → Login direto                                         │
   └─────────────────────────────────────────────────────────┘

   ┌─────────────────────────────────────────────────────────┐
   │ CENÁRIO 3: Usuário não existe                          │
   │ → Cria usuário (sem senha)                             │
   │ → Cria provider GOOGLE                                 │
   │ → Cria conta pessoal                                   │
   │ → Cria vínculo OWNER                                   │
   │ → Gera credenciais + JWT                               │
   └─────────────────────────────────────────────────────────┘

4. Retorna dados de acesso
```

### Erros

```json
{
  "success": false,
  "error": true,
  "message": "auth_err",
  "data": "<error_type>",
  "statusCode": "<int_status_code>"
}
```

| error_type | statusCode | Descrição |
|---|---|---|
| `payload_invalid` | 400 | Body inválido ou campo credential ausente |
| `credential_invalid` | 401 | ID Token do Google inválido, expirado ou audience incorreta |
| `err_provider_google` | 401 | Erro na verificação do token junto ao Google |
| `not_exist_account_active` | 404 | Usuário existe mas nenhuma conta ativa vinculada |
| `email_already_exists` | 409 | Email já cadastrado com provider diferente |
| `user_id_max_retries` | 500 | Falha ao gerar user_id único |
| `account_id_max_retries` | 500 | Falha ao gerar account_id único |
| `token_credentials_max_retries` | 500 | Falha ao gerar token de credenciais |
| `not_created_user` | 500 | Usuário não foi inserido |
| `not_created_account` | 500 | Conta não foi inserida |
| `not_created_account_members` | 500 | Vínculo não foi inserido |
| `not_created_credentials` | 500 | Credenciais não foram inseridas |
| `err_insert` | 500 | Erro genérico de inserção |

### Sucesso — Cenário 1 e 2 (login)

```json
{
  "success": true,
  "error": false,
  "message": "auth_success",
  "data": {
    "user_id": "550e8400-e29b-41d4-a716-446655440000",
    "account_id": "660e8400-e29b-41d4-a716-446655440001",
    "account_name": "conta_a1b2c3d4",
    "role": "OWNER",
    "account_type": "PERSONAL",
    "token": "550e8400...@660e8400...@rfs_abc123",
    "expired": "2026-10-01T03:00:00.000Z",
    "jwt": "eyJhbGciOiJIUzI1NiIs...",
    "accounts": {
      "660e8400-e29b-41d4-a716-446655440001": {
        "account_id": "660e8400-e29b-41d4-a716-446655440001",
        "account_type": "PERSONAL",
        "membership_status": "ACTIVE",
        "name_account": "conta_a1b2c3d4",
        "role": "OWNER",
        "user_id": "550e8400-e29b-41d4-a716-446655440000"
      }
    }
  },
  "statusCode": 200
}
```

### Sucesso — Cenário 3 (criação de conta)

```json
{
  "success": true,
  "error": false,
  "message": "auth_success",
  "data": {
    "user_id": "770e8400-e29b-41d4-a716-446655440002",
    "account_id": "880e8400-e29b-41d4-a716-446655440003",
    "account_name": "conta_e5f6g7h8",
    "role": "OWNER",
    "account_type": "PERSONAL",
    "token": "770e8400...@880e8400...@rfs_xyz789",
    "expired": "2026-10-01T03:00:00.000Z",
    "jwt": "eyJhbGciOiJIUzI1NiIs...",
    "accounts": {
      "880e8400-e29b-41d4-a716-446655440003": {
        "account_id": "880e8400-e29b-41d4-a716-446655440003",
        "account_type": "PERSONAL",
        "membership_status": "ACTIVE",
        "name_account": "conta_e5f6g7h8",
        "role": "OWNER",
        "user_id": "770e8400-e29b-41d4-a716-446655440002"
      }
    }
  },
  "statusCode": 200
}
```

### Payload do Google ID Token (extraído)

| Campo | Descrição |
|---|---|
| sub | ID único do Google (usado como provider_user_id) |
| email | Email do usuário Google |
| name | Nome completo |
| picture | URL da foto de perfil |
| email_verified | true/false |

### Configuração Necessária

| Item | Onde |
|---|---|
| GOOGLE_CLIENT_ID | `.env` — Client ID do Google Cloud Console |
| Authorized origins | Google Cloud Console → Credentials → OAuth 2.0 |
| Redirect URIs | Google Cloud Console → Credentials → OAuth 2.0 |

### Notas

- **CSRF:** O frontend deve enviar o `g_csrf_token` via cookie (automático com GIS)
- ** providers:** O provider `GOOGLE` é vinculado automaticamente no primeiro login
- **Conta pessoal:** Uma conta `PERSONAL` é criada automaticamente com role `OWNER`
- **Rollback:** Se qualquer etapa falhar, a transação é revertida
