# Back-end — Gerência de Sócios CTG

API REST desenvolvida em PHP para gerenciamento de sócios, dependentes, autenticação de usuários e demais recursos do sistema do CTG.

## Funcionalidades implementadas

### Dependentes

Foram implementadas e ajustadas as seguintes funcionalidades relacionadas aos dependentes:

- CRUD completo de dependentes.
- Criação de dependentes vinculados a um sócio titular.
- Busca de todos os dependentes.
- Busca de dependente por ID.
- Busca de dependentes por sócio titular utilizando:

```text
GET /api/dependentes?socio_titular_id={id}
```

- Atualização de dependentes.
- Exclusão de dependentes.
- Campo `status` para controle da situação do dependente.
- Cálculo e controle da maioridade do dependente.
- Atualização automática do status quando o dependente atinge a maioridade.
- Sincronização da situação dos dependentes com o status do sócio titular.
- Regras para impedir situações inconsistentes entre sócios e seus dependentes.

### Status do dependente

O dependente possui um status próprio, que pode ser atualizado conforme as regras de negócio.

Também foi implementada uma lógica de sincronização para verificar dependentes que atingiram a maioridade.

### Maioridade

A maioridade é calculada a partir da data de nascimento do dependente.

Foram adicionadas regras para:

- Identificar dependentes que atingiram a maioridade.
- Atualizar o status automaticamente quando necessário.
- Manter os dados consistentes nas consultas de dependentes.

### Telefone do dependente

Foi incluído e ajustado o suporte ao campo:

```text
telefone
```

nas operações de criação, atualização e persistência de dependentes.

---

## Banco de Dados

Foram criadas as seguintes migrations:

```text
src/Database/migrations/002_dependente_status.sql
src/Database/migrations/003_dependente_maioridade.sql
src/Database/migrations/004_dependente_telefone.sql
```

Essas migrations adicionam as alterações necessárias para suportar:

1. Status dos dependentes.
2. Controle de maioridade.
3. Campo de telefone.

Também foram atualizados:

```text
src/Database/schema.sql
src/Database/seed.sql
```

para manter a estrutura do banco atualizada.

---

# Instalação do projeto

## Pré-requisitos

Antes de instalar o projeto, certifique-se de possuir:

- PHP 8 ou superior.
- MySQL ou MariaDB.
- Git.
- Um servidor local compatível com PHP.

Opcionalmente, pode ser utilizado:

- WampServer.
- XAMPP.
- Laragon.

---

## 1. Clonar o repositório

Para clonar o repositório:

```bash
git clone URL_DO_REPOSITORIO
```

Entre na pasta do projeto:

```bash
cd back-gerencia-socios-ctg
```

Se estiver utilizando o fork, utilize a URL do seu próprio repositório no GitHub.

---

## 2. Configurar o banco de dados

Crie um banco de dados MySQL chamado:

```text
ctg
```

Depois, execute o arquivo principal de estrutura:

```text
src/Database/schema.sql
```

Caso o projeto possua dados iniciais necessários, execute também:

```text
src/Database/seed.sql
```

Em seguida, execute as migrations de dependentes:

```text
src/Database/migrations/002_dependente_status.sql
src/Database/migrations/003_dependente_maioridade.sql
src/Database/migrations/004_dependente_telefone.sql
```

A ordem recomendada é:

```text
1. schema.sql
2. seed.sql
3. 002_dependente_status.sql
4. 003_dependente_maioridade.sql
5. 004_dependente_telefone.sql
```

---

## 3. Configurar a conexão com o banco

Verifique os arquivos de configuração do projeto e configure as informações de conexão com o banco de dados.

Normalmente, será necessário informar:

```text
Host: localhost
Banco: ctg
Usuário: root
Senha: sua_senha
```

As configurações devem corresponder à instalação local do MySQL ou MariaDB.

---

## 4. Iniciar o servidor PHP

Dentro da pasta principal do projeto, execute:

```bash
php -S localhost:8000 index.php
```

O servidor será iniciado em:

```text
http://localhost:8000
```

A API estará disponível em:

```text
http://localhost:8000/api
```

---

# Principais endpoints

## Dependentes

### Listar dependentes

```http
GET /api/dependentes
```

### Buscar dependente por ID

```http
GET /api/dependentes/{id}
```

### Buscar dependentes de um sócio titular

```http
GET /api/dependentes?socio_titular_id={id}
```

### Criar dependente

```http
POST /api/dependentes
Content-Type: application/json
X-Auth-Token: {token}
```

Exemplo:

```json
{
    "socio_titular_id": 1,
    "nome_completo": "Teste Dependente",
    "cpf": "999.111.222-33",
    "telefone": "51999999999",
    "foto": null,
    "data_nascimento": "2015-06-15",
    "dancarino": false
}
```

### Atualizar dependente

```http
PUT /api/dependentes/{id}
Content-Type: application/json
X-Auth-Token: {token}
```

### Excluir dependente

```http
DELETE /api/dependentes/{id}
X-Auth-Token: {token}
```

---

# Autenticação

A API utiliza autenticação por token.

O login pode ser realizado através do endpoint:

```http
POST /api/auth/login
```

Exemplo:

```json
{
    "email": "admin@ctg.local",
    "senha": "admin123"
}
```

Após o login, o token retornado deve ser utilizado nas requisições protegidas:

```http
X-Auth-Token: {access_token}
```

---

# Testes HTTP

Foram atualizados arquivos `.http` para facilitar os testes da API:

```text
test_auth.http
test_dependentes.http
test_socios.http
test_usuarios.http
```

Os testes podem ser executados utilizando extensões compatíveis com arquivos HTTP, como:

- REST Client para VS Code.
- JetBrains HTTP Client.

## Fluxo recomendado para testar dependentes

1. Realizar login como administrador.
2. Armazenar o token retornado.
3. Listar os dependentes.
4. Criar um dependente.
5. Capturar o ID retornado.
6. Buscar o dependente criado.
7. Atualizar o dependente.
8. Buscar novamente para confirmar a atualização.
9. Testar dependentes vinculados a um sócio titular.
10. Testar as regras relacionadas à maioridade.

---

# Estrutura das principais alterações

As alterações foram realizadas principalmente nos seguintes arquivos:

```text
src/Controller/DependenteController.php
src/Database/schema.sql
src/Database/seed.sql
src/Database/migrations/002_dependente_status.sql
src/Database/migrations/003_dependente_maioridade.sql
src/Database/migrations/004_dependente_telefone.sql
src/Http/Request.php
src/Model/Dependente.php
src/Repository/DependenteRepository.php
src/Service/DependenteService.php
src/Service/SocioService.php
```

---

# Controle de versão

As alterações relacionadas à implementação foram realizadas na branch:

```text
dependente-angelina
```

O commit principal foi:

```text
feat: implementa CRUD e regras de dependentes
```

O fluxo recomendado para colaboração é:

```text
Fork do repositório original
        ↓
Branch de desenvolvimento individual
        ↓
Commit das alterações
        ↓
Push para o fork
        ↓
Pull Request
```

---

# Fluxo para outro colaborador continuar o trabalho

Para outra pessoa trabalhar a partir deste repositório, ela pode clonar o fork:

```bash
git clone URL_DO_SEU_FORK
```

Depois:

```bash
cd back-gerencia-socios-ctg
```

E acessar a branch de desenvolvimento:

```bash
git switch dependente-angelina
```

Caso a branch ainda não esteja disponível localmente:

```bash
git fetch origin
git switch --track origin/dependente-angelina
```

---

# Observações

Antes de realizar um commit, recomenda-se executar:

```bash
git status
```

Para verificar alterações de formatação:

```bash
git diff --check
```

Para verificar as alterações que estão preparadas para o commit:

```bash
git diff --cached --stat
```

Após a revisão:

```bash
git add .
git commit -m "mensagem do commit"
git push
```

---

## Status da implementação

As funcionalidades relacionadas ao CRUD e às regras de negócio dos dependentes foram implementadas e enviadas para a branch de desenvolvimento.

O projeto está preparado para continuar o desenvolvimento e para integração das alterações por meio de Pull Request.