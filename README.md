# SafeScribe — API REST com Autenticação e Autorização via JWT (.NET 8)

Plataforma para **gestão de notas/documentos sensíveis** com **segurança** e **controle de acesso** baseados em **JSON Web Tokens (JWT)**. Este repositório implementa o núcleo da API exigido no enunciado da FIAP (CP5), com **roles**, **DTOs**, **políticas de autorização** e (opcionalmente) **logout com blacklist**.

> **Resumo:** Usuários autenticados interagem com suas próprias notas; administradores possuem privilégios totais. Tokens JWT são emitidos no login e validados em cada requisição protegida.


---

## 🚀 Stack e Requisitos

- **.NET SDK**: 8.0+
- **Projeto**: ASP.NET Core Web API
- **Autenticação**: `Microsoft.AspNetCore.Authentication.JwtBearer`
- **Persistência**: EF Core InMemory (ou EF Core + SQLite/SQL Server)—à sua escolha
- **Hash de senha**: `BCrypt.Net-Next` (ou similar)
- **Swagger**: `Swashbuckle.AspNetCore`

---

## 🎯 Endpoints

### AuthController
- `POST /api/v1/auth/registrar`
  - Corpo: `UserRegisterDto`
  - Ação: cria usuário com **hash** de senha
- `POST /api/v1/auth/login`
  - Corpo: `LoginRequestDto`
  - Ação: valida credenciais e retorna **JWT**

### NotasController (protegido por `[Authorize]`)
- `POST /api/v1/notas` — **[Authorize(Roles = "Editor,Admin")]**
  - Corpo: `NoteCreateDto`
  - **UserId** vem da claim do token, **não** do body
- `GET /api/v1/notas/{id}`
  - **Leitor/Editor**: apenas notas do próprio usuário
  - **Admin**: qualquer nota
- `PUT /api/v1/notas/{id}`
  - **Editor**: apenas notas do próprio usuário
  - **Admin**: qualquer nota
- `DELETE /api/v1/notas/{id}` — **[Authorize(Roles = "Admin")]**

> **Swagger** deverá mostrar os endpoints; use o botão **Authorize** para colar o token (`Bearer {{token}}`).


---

## 🧪 Como rodar e testar

```bash
# 1) Restaurar e compilar
dotnet restore
dotnet build

# 2) Executar
dotnet run
# ou
dotnet watch run

# API padrão: http://localhost:5000 ou https://localhost:7000 (conforme o launchSettings.json)
```

### Fluxo de teste (Postman/HTTPie)
1. **Registrar**: `POST /api/v1/auth/registrar` com `{"username":"ana","password":"123","role":"Editor"}`
2. **Login**: `POST /api/v1/auth/login` → copie o **token JWT**
3. **Authorize** no Swagger (ou use `Authorization: Bearer {{token}}`)
4. **Criar Nota** (Editor/Admin): `POST /api/v1/notas`
5. **Obter/Editar/Excluir** de acordo com as regras de autorização


---
## 👥 Integrantes 
- **Nome:Gabriel Gomes Mancera — RM:555427
**Turma**: 2TDSPX — FIAP

---
