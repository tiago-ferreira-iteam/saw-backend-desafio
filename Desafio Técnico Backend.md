# **SAW - Desafio Técnico - Backend - API de Produtos**

O objetivo deste teste é implementar uma API RESTful para gerenciar produtos. A API deve permitir que usuários autenticados realizem operações CRUD (Criar, Ler, Atualizar e Excluir) em produtos, com validações robustas e controle de acesso baseado em roles.

---

## **Requisitos Funcionais**

### **1. Modelo de Dados**
**1.1 Cada produto deve ter os seguintes campos:**

| Campo         | Tipo            | Obrigatório | Validação                                                                                     | Descrição                          |
|---------------|-----------------|-------------|-----------------------------------------------------------------------------------------------|------------------------------------|
| `id`          | `String(UUID)`  | Não         | Gerado automaticamente pelo backend.                                                          | Identificador único do produto.   |
| `name`        | `String`        | Sim         | Tamanho mínimo: 3 caracteres<br>Tamanho máximo: 100 caracteres.                               | Nome do produto.                  |
| `description` | `String`        | Sim         | Tamanho mínimo: 10 caracteres<br>Tamanho máximo: 500 caracteres.                              | Descrição detalhada do produto.   |
| `price`       | `BigDecimal`    | Sim         | Valor mínimo: 0.01<br>Valor máximo: 999999.99.                                                | Preço do produto.                 |
| `category`    | `Categoria`     | Sim         | Deve pertencer a uma lista pré-definida de categorias válidas (ex.: "Eletrônicos", "Roupas", "Alimentos"). | Categoria do produto.             |
| `stock`       | `Integer`       | Sim         | Valor mínimo: 0<br>Valor máximo: 10000.                                                       | Quantidade em estoque do produto. |
| `createdAt`   | `OffsetDateTime`| Não         | Preenchida automaticamente no momento do cadastro.                                            | Data e hora da criação do produto.|
| `updatedAt`   | `OffsetDateTime`| Não         | Preenchida automaticamente sempre que o produto for atualizado.                              | Data e hora da última modificação.|

**1.2 Cada categoria deve ter os seguintes campos:**

| Campo         | Tipo            | Obrigatório | Validação                                                                                     | Descrição                          |
|---------------|-----------------|-------------|-----------------------------------------------------------------------------------------------|------------------------------------|
| `id`          | `String(UUID)`  | Não         | Gerado automaticamente pelo backend.                                                          | Identificador único da categoria.   |
| `name`        | `String`        | Sim         | Tamanho mínimo: 3 caracteres<br>Tamanho máximo: 50 caracteres.                               | Nome da categoria.
| `createdAt`   | `OffsetDateTime`| Não         | Preenchida automaticamente no momento do cadastro.                                            | Data e hora da criação da categoria.|
| `updatedAt`   | `OffsetDateTime`| Não         | Preenchida automaticamente sempre que o categoria for atualizado.                              | Data e hora da última modificação.|


**1.3 Cada Usuário deve ter os seguintes campos:**

| Campo         | Tipo            | Obrigatório | Validação                                                                                     | Descrição                          |
|---------------|-----------------|-------------|-----------------------------------------------------------------------------------------------|------------------------------------|
| `id`          | `String(UUID)`  | Não         | Gerado automaticamente pelo backend.                                                          | Identificador único do usuário.   |
| `name`        | `String`        | Sim         | Deve conter entre 3 e 100 caracteres.<br> Não pode conter caracteres especiais inválidos (ex.: @ , # , $ ).                               | Nome completo do usuário.
| `email`       | `String`        | Sim         | Deve ser um e-mail válido no formato "usuario@dominio.com".<br> Deve ser único no sistema.                                            | Endereço de e-mail do usuário.|
| `password`    | `String`        | Sim         | Deve ter no mínimo 8 caracteres.<br> Deve conter letras maiúsculas, minúsculas, números e caracteres especiais.<br> A senha deve ser armazenada criptografada no banco de dados.                              | Senha criptografada do usuário (não retornada na resposta).|
| `role`        | `String`        | Sim         | Valores permitidos: "admin" , "user" .<br> Valor padrão: "user" .                             | Papel ou função do usuário no sistema.|
| `isActive`    | `Boolean`       | Não         | Valor padrão: true .                                                                           | Indica se a conta do usuário está ativa ( true ) ou inativa ( false ).|
| `createdAt`   | `OffsetDateTime`| Não         | Preenchida automaticamente no momento do cadastro.                                           | Data e hora da criação da usuário.|
| `updatedAt`   | `OffsetDateTime`| Não         | Preenchida automaticamente sempre que o usuário for atualizado.                              | Data e hora da última modificação.|

**1.4 Falhas de validação é composto pelos seguintes campos:**

| Campo       | Tipo           | Descrição                                                                                   |
|-------------|----------------|---------------------------------------------------------------------------------------------|
| `title`     | `String`       | Título geral da falha. Ex.: "Falhas de validação".                                          |
| `status`    | `Integer`      | Código HTTP correspondente ao erro. Ex.: `400` para erros de validação.                     |
| `detail`    | `String`       | Descrição geral do problema. Ex.: "Um ou mais campos estão inválidos".                      |
| `errors`    | `Array<Error>`| Lista de objetos contendo detalhes sobre cada campo inválido.                              |

**1.5 Cada objeto dentro do array `errors` representa um campo específico que falhou na validação. A estrutura é composta pelos seguintes campos:**

| Campo       | Tipo     | Descrição                                                                                   |
|-------------|----------|---------------------------------------------------------------------------------------------|
| `field`     | `String` | Nome do campo que falhou na validação. Ex.: "name", "price".                                |
| `message`   | `String` | Mensagem de erro específica para o campo. Ex.: "O campo 'name' deve ter entre 3 e 100 caracteres.". |

## **Exemplo de Resposta**

Abaixo está um exemplo de resposta JSON que utiliza o modelo de dados descrito acima:

```json
{
  "title": "Falhas de validação",
  "status": 400,
  "detail": "Um ou mais campos estão inválidos",
  "errors": [
    {
      "field": "name",
      "message": "O campo 'name' deve ter entre 3 e 100 caracteres."
    },
    {
      "field": "price",
      "message": "O campo 'price' deve ser maior que 0.01 e menor que 999999.99."
    }
  ]
}
```

### **2. Produtos - Operações CRUD**

#### **2.1. Criar Produto**
- **Endpoint:** `POST /api/products`
- **Descrição:** Permite que um usuário autenticado crie um novo produto.
- **Validações:**
  - Todos os campos obrigatórios devem ser preenchidos.
  - As validações específicas para cada campo estão descritas na tabela acima.
- **Resposta Esperada:**
  - Em caso de sucesso:
    ```json
    {
      "id": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
      "name": "Smartphone X",
      "description": "Um smartphone top de linha.",
      "price": 1500.00,
      "category": "Eletrônicos",
      "stock": 50,
      "createdAt": "2023-10-01T10:00:00Z",
      "updatedAt": "2023-10-01T10:00:00Z"
    }
    ```
  - Em caso de falha, retornar mensagens de erro claras conforme formato especificado na secção acima(Modelo de Dados).

---

#### **2.2. Buscar Produto por ID**
- **Endpoint:** `GET /api/products/{id}`
- **Descrição:** Permite buscar um produto específico pelo seu `id`.
- **Validações:**
  - O `id` fornecido deve ser um UUID válido.
  - O produto com o `id` especificado deve existir no banco de dados.
- **Resposta Esperada:**
  - Em caso de sucesso:
    ```json
    {
      "id": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
      "name": "Smartphone X",
      "description": "Um smartphone top de linha.",
      "price": 1500.00,
      "category": "Eletrônicos",
      "stock": 50,
      "createdAt": "2023-10-01T10:00:00Z",
      "updatedAt": "2023-10-01T10:00:00Z"
    }
    ```
  - Em caso de falha, retornar mensagens de erro claras conforme formato especificado na secção acima(Modelo de Dados).

---

#### **2.3. Atualizar Produto**
- **Endpoint:** `PUT /api/products/{id}`
- **Descrição:** Permite atualizar os dados de um produto existente.
- **Validações:**
  - O `id` fornecido deve ser um UUID válido.
  - O produto com o `id` especificado deve existir no banco de dados.
  - Os campos enviados devem respeitar as mesmas validações definidas no modelo de dados.
- **Resposta Esperada:**
  - Em caso de sucesso:
    ```json
    {
      "id": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
      "name": "Smartphone X Pro",
      "description": "Um smartphone top de linha com novos recursos.",
      "price": 1600.00,
      "category": "Eletrônicos",
      "stock": 45,
      "createdAt": "2023-10-01T10:00:00Z",
      "updatedAt": "2023-10-02T12:30:00Z"
    }
    ```
  - Em caso de falha, retornar mensagens de erro claras conforme formato especificado na secção acima(Modelo de Dados).

---

#### **2.4. Excluir Produto**
- **Endpoint:** `DELETE /api/products/{id}`
- **Descrição:** Permite excluir um produto existente.
- **Validações:**
  - O `id` fornecido deve ser um UUID válido.
  - O produto com o `id` especificado deve existir no banco de dados.
- **Resposta Esperada:**
  - Em caso de sucesso: Retornar apenas o http status correto.
  - Em caso de falha, retornar mensagens de erro claras conforme formato especificado na secção acima(Modelo de Dados).

---

#### **2.5. Pesquisar Produtos**
- **Endpoint:** `GET /api/products`
- **Descrição:** Permite listar produtos com filtros opcionais.
- **Parâmetros de Consulta:**
Abaixo estão os parâmetros de consulta disponíveis para este endpoint, juntamente com suas regras de validação:

  | Parâmetro   | Tipo        | Obrigatório | Descrição                                                                                   | Regras de Validação                                                                                                                                               
  |-------------|-------------|-------------|---------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------
  | `category`  | `String`    | Não         | Filtra produtos por categoria.                                                              | - Deve ser uma string válida correspondente a uma categoria existente.<br>- Não pode ser vazia ou conter caracteres especiais inválidos (ex.: `@`, `#`, `$`).     
  | `minPrice`  | `BigDecimal`    | Não         | Filtra produtos com preço igual ou superior ao valor informado.                             | - Deve ser um número positivo ou zero.<br>- Não pode ser maior que `maxPrice` (se ambos forem fornecidos).                                                        
  | `maxPrice`  | `BigDecimal`    | Não         | Filtra produtos com preço igual ou inferior ao valor informado.                             | - Deve ser um número positivo ou zero.<br>- Não pode ser menor que `minPrice` (se ambos forem fornecidos).                                                        
  | `sortBy`    | `String`    | Não         | Ordena os resultados por um campo específico.                                               | - Valores permitidos: `name`, `price`, `createdAt`.<br>- Caso o valor seja inválido, o sistema deve retornar um erro indicando os valores aceitos.                
  | `order`     | `String`    | Não         | Define a ordem da ordenação.                                                                | - Valores permitidos: `asc` (ascendente) ou `desc` (descendente).<br>- Caso o valor seja inválido, o sistema deve retornar um erro indicando os valores aceitos.  
  | `page`      | `integer`   | Não         | Número da página atual para paginação.                                                      | - Deve ser um número inteiro positivo.<br>- Valor padrão: `1`.<br>- Caso o valor seja inválido ou menor que `1`, o sistema deve ajustar automaticamente para `1`. 
  | `pageSize`  | `integer`   | Não         | Número de itens por página.   
- **Resposta Esperada:**
  - Em caso de sucesso:
    ```json
        {
          "content": [
            {
              "id": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
              "name": "Smartphone X",
              "description": "Um smartphone top de linha.",
              "price": 1500.00,
              "category": "Eletrônicos",
              "stock": 50,
              "createdAt": "2023-10-01T10:00:00Z",
              "updatedAt": "2023-10-01T10:00:00Z"
            },
            {
              "id": "c9bf9e57-1685-4c89-bafb-ff5af830be8a",
              "name": "Camiseta Y",
              "description": "Uma camiseta confortável.",
              "price": 50.00,
              "category": "Roupas",
              "stock": 200,
              "createdAt": "2023-10-01T11:00:00Z",
              "updatedAt": "2023-10-01T11:00:00Z"
            }
          ],
          "size": 2,
          "total_elements": 2,
          "total_pages": 1,
          "page_number": 1
        }
    ```
  - Em caso de falha, retornar mensagens de erro claras conforme formato especificado na secção acima(Modelo de Dados).

---

### **3. Categorias - Operações CRUD**

#### **2.5. Listar Categorias**
- **Endpoint:** `GET /api/categories`
- **Descrição:** Permite listar as categorias disponíveis.
- **Parâmetros de Consulta:**
Não é necessário filtros ou paginação.
 
- **Resposta Esperada:**
  - Em caso de sucesso:
    ```json
        [
            {
              "id": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
              "name": "Eletrônicos",
              "createdAt": "2023-10-01T10:00:00Z",
              "updatedAt": "2023-10-01T10:00:00Z"
            },
            {
              "id": "c9bf9e57-1685-4c89-bafb-ff5af830be8a",
              "name": "Roupas",
              "createdAt": "2023-10-01T11:00:00Z",
              "updatedAt": "2023-10-01T11:00:00Z"
            }
        ]
    ```
  - Em caso de falha, retornar mensagens de erro claras conforme formato especificado na secção acima(Modelo de Dados).

---

### **4. Autenticação e Autorização**
- **Autenticação:**
  - Utilizar Autenticação Basic para validar usuários.
  - Apenas usuários autenticados podem acessar os endpoints da API.

- **Autorização:**
  - Implementar controle de acesso baseado em roles (admin e usuário comum):
    - **Admins:** Têm permissão para realizar todas as operações CRUD.
    - **Usuários comuns:** Podem apenas listar dados.

---

### **5. Carga Inicial de Dados**

- Nem usuários nem categorias poderão ser gerenciados via API.

- `Usuários:` Serão criados diretamente na base de dados durante a inicialização. Não haverá endpoints para criar, atualizar ou excluir usuários.
- `Categorias:` Serão inseridas na inicialização e não poderão ser criadas, atualizadas ou excluídas via API.
- Endpoints disponíveis:
  - Listar todas as categorias (somente leitura).


- Ao iniciar a aplicação, carregar uma lista pré-definida de:
  - `Categorias` válidas na base de dados.
  - `Usuários` válidos na base de dados.
- Criar usuários para utilização com diferentes roles:
  - Usuário Admin (`admin`): Possui permissão para executar todas as ações no sistema.
  - Usuário Comum (`user`): Possui permissão apenas para leitura de dados.

---

### **6. Tratamento de Exceções**
- O tratamento de exceções da aplicação deve ser centralizada em um GlobalExceptionHandler utilizando @RestControllerAdvice.

---

### **7. Padrões de Desenvolvimento**
- A aplicação deve seguir a arquitetura MVC, garantindo uma separação clara entre controladores, serviços e repositórios. Além disso, todas as interações da API devem utilizar DTOs (Data Transfer Objects) para evitar a exposição direta das entidades do banco de dados e garantir uma melhor organização e segurança dos dados trafegados.
- A implementação do sistema deve ser inteiramente realizada em inglês, incluindo código, comentários, nomes de variáveis, mensagens de log e erros.
---

### **8. Containerização**
- Recomenda-se o uso de Docker Compose para facilitar a orquestração dos serviços, como banco de dados e outros componentes necessários para o funcionamento da aplicação.

---

### **Entrega da solução**
 - O projeto deve ser entregue através de um repositório no **GitHub**, garantindo versionamento adequado e acesso facilitado ao código. O repositório deve conter um **README detalhado**, descrevendo a aplicação, seus requisitos, tecnologias utilizadas e instruções claras sobre como configurá-la e executá-la. O README deve incluir etapas para **clonar o repositório, configurar dependências, executar a aplicação e qualquer outra informação relevante** para a utilização do sistema.

### **Critérios de Avaliação**

1. **Qualidade do Código:**
   - Código limpo, organizado e bem documentado.
   - Seguir boas práticas de programação.

2. **Funcionalidades Implementadas:**
   - Todas as operações CRUD devem estar funcionando corretamente.
   - Validações robustas para garantir a integridade dos dados.

3. **Segurança:**
   - Validação e controle de acesso baseado em roles.

4. **Testes:**
   - Cobertura de testes unitários.

5. **Desempenho:**
   - Eficiência na manipulação de dados.
   - Tratamento adequado de erros e exceções.

6. **Criatividade:**
   - Soluções inovadoras ou extras além do escopo básico.

---

### **Ferramentas e Tecnologias Recomendadas**

1. **Backend:**
   - Java 17
   - Spring Boot
   - Spring Data MongoDB
   - Spring Security

2. **Banco de Dados:**
   - MongoDB

4. **Docker Compose:**
   - Para executar uma instância da base de dados MongoDB e qualquer outra ferramenta utilizada na aplicação.

5. **Testes:**
   - JUnit 5
   - Mockito

---