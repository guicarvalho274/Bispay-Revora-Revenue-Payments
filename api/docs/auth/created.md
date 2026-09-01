Cria uma nova conta de usuário com autenticação por senha.

```json
{
  "email": "usuario@email.com",
  "name": "João Silva",
  "password": "Senha@123",
  "cpf": "12345678901",        // opcional
  "phone": "11 9 1234-5678"   // opcional
}
```

Validações (Zod)

| Campo | Regra | Erro |
| --- | --- | --- |
| email | obrigatório, formato válido | email_invalid |
| name | obrigatório, min 1, cada parte min 2 letras | name_required / name_obrigatoried / name_invalid |
| password | min 8, maiúscula, minúscula, número, especial | password_min_length / password_necessarie_uppercase / password_necessarie_lowercase / password_necessarie_number / password_necessarie_especial |
| cpf | opcional, 11 dígitos válidos | cpf_invalid |
| phone | opcional, formato 00 0 0000-0000 | phone_invalid |

```json
Erros
{
  "success": false,
  "error": true,
  "message": "auth_err",
  "data": "<error_type>",
  "statusCode": "<int_status_code>"
}
```

<table>
<tbody>
<tr>
<td colspan="1" rowspan="1">
<p>error_type</p>
</td>
<td colspan="1" rowspan="1">
<p>statusCode</p>
</td>
<td colspan="1" rowspan="1">
<p>Descrição</p>
</td>
</tr>
<tr>
<td colspan="1" rowspan="1">
<p>payload_invalid</p>
</td>
<td colspan="1" rowspan="1">
<p>400</p>
</td>
<td colspan="1" rowspan="1">
<p>Body inválido ou campos obrigatórios ausentes</p>
</td>
</tr>
<tr>
<td colspan="1" rowspan="1">
<p>email_already_exists</p>
</td>
<td colspan="1" rowspan="1">
<p>409</p>
</td>
<td colspan="1" rowspan="1">
<p>Email já cadastrado</p>
</td>
</tr>
<tr>
<td colspan="1" rowspan="1">
<p>cpf_already_exists</p>
</td>
<td colspan="1" rowspan="1">
<p>409</p>
</td>
<td colspan="1" rowspan="1">
<p>CPF já cadastrado</p>
</td>
</tr>
<tr>
<td colspan="1" rowspan="1">
<p>phone_already_exists</p>
</td>
<td colspan="1" rowspan="1">
<p>409</p>
</td>
<td colspan="1" rowspan="1">
<p>Telefone já cadastrado</p>
</td>
</tr>
<tr>
<td colspan="1" rowspan="1">
<p>email_already_exists_account</p>
</td>
<td colspan="1" rowspan="1">
<p>409</p>
</td>
<td colspan="1" rowspan="1">
<p>Email já utilizado em outra conta</p>
</td>
</tr>
<tr>
<td colspan="1" rowspan="1">
<p>document_already_exists_account</p>
</td>
<td colspan="1" rowspan="1">
<p>409</p>
</td>
<td colspan="1" rowspan="1">
<p>Documento já utilizado em outra conta</p>
</td>
</tr>
<tr>
<td colspan="1" rowspan="1">
<p>phone_already_exists_account</p>
</td>
<td colspan="1" rowspan="1">
<p>409</p>
</td>
<td colspan="1" rowspan="1">
<p>Telefone já utilizado em outra conta</p>
</td>
</tr>
<tr>
<td colspan="1" rowspan="1">
<p>user_id_max_retries</p>
</td>
<td colspan="1" rowspan="1">
<p>500</p>
</td>
<td colspan="1" rowspan="1">
<p>Falha ao gerar user_id único após múltiplas tentativas</p>
</td>
</tr>
<tr>
<td colspan="1" rowspan="1">
<p>account_id_max_retries</p>
</td>
<td colspan="1" rowspan="1">
<p>500</p>
</td>
<td colspan="1" rowspan="1">
<p>Falha ao gerar account_id único após múltiplas tentativas</p>
</td>
</tr>
<tr>
<td colspan="1" rowspan="1">
<p>token_credentials_max_retries</p>
</td>
<td colspan="1" rowspan="1">
<p>500</p>
</td>
<td colspan="1" rowspan="1">
<p>Falha ao gerar token de credenciais</p>
</td>
</tr>
<tr>
<td colspan="1" rowspan="1">
<p>not_created_user</p>
</td>
<td colspan="1" rowspan="1">
<p>500</p>
</td>
<td colspan="1" rowspan="1">
<p>Usuário não foi inserido no banco</p>
</td>
</tr>
<tr>
<td colspan="1" rowspan="1">
<p>not_created_account</p>
</td>
<td colspan="1" rowspan="1">
<p>500</p>
</td>
<td colspan="1" rowspan="1">
<p>Conta não foi inserida no banco</p>
</td>
</tr>
<tr>
<td colspan="1" rowspan="1">
<p>not_created_account_members</p>
</td>
<td colspan="1" rowspan="1">
<p>500</p>
</td>
<td colspan="1" rowspan="1">
<p>Vínculo usuário-conta não foi inserido</p>
</td>
</tr>
<tr>
<td colspan="1" rowspan="1">
<p>not_created_credentials</p>
</td>
<td colspan="1" rowspan="1">
<p>500</p>
</td>
<td colspan="1" rowspan="1">
<p>Credenciais não foram inseridas</p>
</td>
</tr>
<tr>
<td colspan="1" rowspan="1">
<p>duplicate_entry</p>
</td>
<td colspan="1" rowspan="1">
<p>500</p>
</td>
<td colspan="1" rowspan="1">
<p>Entrada duplicada genérica</p>
</td>
</tr>
<tr>
<td colspan="1" rowspan="1">
<p>err_insert</p>
</td>
<td colspan="1" rowspan="1">
<p>500</p>
</td>
<td colspan="1" rowspan="1">
<p>Erro genérico de inserção (produção)</p>
</td>
</tr>
</tbody></table>

```json
Sucesso
{
  "success": true,
  "error": false,
  "message": "created_account",
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

Fluxo Interno

1. Valida payload com Zod
2. Hash da senha com bcrypt (10 rounds)
3. Cria usuário (users)
4. Cria provider PASSWORD (user_auth_providers)
5. Cria conta (accounts)
6. Busca role OWNER
7. Cria vínculo usuário-conta (account_members)
8.  Gera token de refresh (credentials_users)
9.   Gera JWT
   10. Retorna tudo junto

   
   
   Nota: Se qualquer etapa falhar, a transação é revertida (rollback) e nenhum dado persiste.