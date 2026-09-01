## `POST /auth/login` — Login com Email/Senha

Autentica usuário existente com email e senha. Retorna tokens de acesso e dados da conta.

### Body

```json
{
  "email": "usuario@email.com",
  "password": "Senha@123"
}
```

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| email | string | sim | Email cadastrado |
| password | string | sim | Senha do usuário (min 8 caracteres) |

### Validações (Zod)

| Campo | Regra | Erro |
|---|---|---|
| email | obrigatório, formato válido | `email_invalid` |
| password | obrigatório, min 8 | `password_min_length` |

### Fluxo Interno

```
1. Valida payload com Zod
2. Busca usuário por email no banco
3. Verifica se usuário existe e tem password_hash
   (usuários Google登录 sem senha retornam erro)
4. Compara senha com bcrypt.compare
5. Busca contas vinculadas (loginAuthProvider)
6. Verifica/cria token de refresh
7. Gera JWT
8. Retorna dados de acesso
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
| `payload_invalid` | 400 | Body inválido ou campos obrigatórios ausentes |
| `user_not_found` | 404 | Email não cadastrado ou senha incorreta |
| `not_exist_account_active` | 404 | Usuário existe mas nenhuma conta ativa vinculada |
| `token_not_found` | 404 | Nenhum token de refresh ativo encontrado |
| `token_expired` | 401 | Token de refresh expirado |
| `err_verify_token` | 500 | Erro ao verificar token de refresh |

### Sucesso

```json
{
  "success": true,
  "error": false,
  "message": "login_success",
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

### Retorno com Múltiplas Contas

```json
{
  "success": true,
  "error": false,
  "message": "login_success",
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
      },
      "990e8400-e29b-41d4-a716-446655440005": {
        "account_id": "990e8400-e29b-41d4-a716-446655440005",
        "account_type": "TEAM",
        "membership_status": "ACTIVE",
        "name_account": "conta_x9y8z7w6",
        "role": "ADMIN",
        "user_id": "550e8400-e29b-41d4-a716-446655440000"
      }
    }
  },
  "statusCode": 200
}
```

### Campos do Retorno

| Campo | Tipo | Descrição |
|---|---|---|
| user_id | UUID | ID único do usuário |
| account_id | UUID | ID da conta de acesso atual |
| account_name | string | Nome da conta |
| role | string | Papel do usuário na conta (OWNER, ADMIN, MEMBER) |
| account_type | string | Tipo da conta (PERSONAL, TEAM) |
| token | string | Token de refresh (userId@accountId@hash) |
| expired | ISO 8601 | Data de expiração do token (+1 mês) |
| jwt | string | JWT de acesso (para requisições autenticadas) |
| accounts | object | Todas as contas vinculadas ao usuário |

### Notas

- **Segurança:** A mensagem de erro `user_not_found` é genérica — não revela se o email existe ou se a senha está errada
- **Google Auth:** Usuários criados via Google não têm `password_hash` e recebem o mesmo erro
- **Conta padrão:** A `current_access` retornada é a conta do tipo `PERSONAL`
- **Rollback:** Se qualquer etapa falhar, a transação é revertida
- **Token:** O `jwt` deve ser enviado no header `Authorization: Bearer <jwt>` para rotas autenticadas
