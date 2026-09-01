## `POST /auth/created-password` — Criar/Senha

Cria ou atualiza a senha de um usuário existente. Requer autenticação e permissão de OWNER na conta.

### Headers

```
Authorization: Bearer <jwt>
```

### Body

```json
{
  "password": "NovaSenha@123"
}
```

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| password | string | sim | Nova senha (min 8 caracteres) |

### Validações (Zod)

| Campo | Regra | Erro |
|---|---|---|
| password | min 8 | `password_min_length` |
| password | maiúscula | `password_necessarie_uppercase` |
| password | minúscula | `password_necessarie_lowercase` |
| password | número | `password_necessarie_number` |
| password | especial (!@#$%^&*) | `password_necessarie_especial` |

### Validações de Autorização

| Verificação | Erro | StatusCode |
|---|---|---|
| JWT válido via accessApi | `token_invalid` / `token_expired` | 401 |
| Role existe no request | `role_name_invalid` | 400 |
| Role é OWNER | `role_not_permission_operation` | 403 |

### Fluxo Interno

```
1. Valida JWT via middleware accessApi
2. Verifica se role é OWNER
3. Valida payload com Zod
4. Hash da nova senha com bcrypt (10 rounds)
5. Verifica se usuário é OWNER da conta (getAccountMemberById)
6. Atualiza password_hash no banco (updatePassword)
7. Cria/Atualiza provider PASSWORD (user_auth_providers)
8. Retorna confirmação
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
| `token_invalid` | 401 | JWT inválido ou ausente |
| `token_expired` | 401 | JWT expirado |
| `role_name_invalid` | 400 | Role não encontrada no token |
| `role_not_permission_operation` | 403 | Usuário não é OWNER da conta |
| `payload_invalid` | 400 | Body inválido ou campo password ausente |
| `password_min_length` | 400 | Senha com menos de 8 caracteres |
| `password_necessarie_uppercase` | 400 | Senha sem letra maiúscula |
| `password_necessarie_lowercase` | 400 | Senha sem letra minúscula |
| `password_necessarie_number` | 400 | Senha sem número |
| `password_necessarie_especial` | 400 | Senha sem caractere especial |
| `not_found_account_member` | 404 | Vínculo usuário-conta não encontrado |
| `account_not_owner` | 403 | Usuário não é OWNER nesta conta |
| `not_updated_password` | 404 | Falha ao atualizar senha no banco |
| `user_provider_already_exists` | 409 | Provider PASSWORD já vinculado |
| `err_update_password` | 500 | Erro genérico ao atualizar senha |

### Sucesso

```json
{
  "success": true,
  "error": false,
  "message": "created_password",
  "data": {
    "updated": true
  },
  "statusCode": 200
}
```

### Casos de Uso

| Cenário | Descrição |
|---|---|
| Primeira senha | Usuário criou conta via Google e agora define senha |
| Alterar senha | Usuário OWNER quer trocar a senha da conta |
| Reset de senha | Admin redefine senha de usuário (mesmo fluxo) |

### Notas

- **OWNER apenas:** Somente o OWNER da conta pode criar/atualizar senhas
- **Provider PASSWORD:** Se já existir, o `createProvider` pode retornar erro — tratar no frontend
- **Rollback:** Se qualquer etapa falhar, a transação é revertida
- **Segurança:** A senha nunca é retornada na resposta
