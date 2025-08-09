# SQL Tutorial
---
[1. Introdução e Sintaxe](#1-introdução-e-sintaxe)
  
[2. Consultas Básicas (SELECT)](#2-consultas-básicas-select)
  
[3. Manipulação de Dados (DML)](#3-manipulação-de-dados-dml)
  
[4. Funções e Operadores](#4-funções-e-operadores)
  
[5. Junções (Joins) e Combinações](#5-junções-joins-e-combinações)
  
[6. Agrupamento e Filtragem](#6-agrupamento-e-filtragem)
  
[7. Avançados](#7-avançados)

[Como Incorporar o Apache Superset em um Site Usando o SDK (Maneira Profissional)](#como-incorporar-o-apache-superset-em-um-site-usando-o-sdk-maneira-profissional)
  
## 1. Introdução e Sintaxe
- 1.1. SQL HOME
- 1.2. SQL Intro
- 1.3. SQL Syntax
---
## 2. Consultas Básicas (SELECT)
- 2.1. SQL Select
- 2.2. SQL Select Distinct
- 2.3. SQL Where
- 2.4. SQL Order By
- 2.5. SQL And
- 2.6. SQL Or
- 2.7. SQL Not
- 2.8. SQL Select Top
---
## 3. Manipulação de Dados (DML)
- 3.1. SQL Insert Into
- 3.2. SQL Update
- 3.3. SQL Delete
- 3.4. SQL Select Into
- 3.5. SQL Insert Into Select
---
## 4. Funções e Operadores
- 4.1. SQL Aggregate Functions
- 4.2. SQL Min and Max
- 4.3. SQL Count
- 4.4. SQL Sum
- 4.5. SQL Avg
- 4.6. SQL Like
- 4.7. SQL Wildcards
- 4.8. SQL In
- 4.9. SQL Between
- 4.10. SQL Aliases
- 4.11. SQL Operators
- 4.12. SQL Null Values
- 4.13. SQL Null Functions
- 4.14. SQL Case
- 4.15. SQL Any, All
- 4.16. SQL Exists
---
## 5. Junções (Joins) e Combinações
- 5.1. SQL Joins
- 5.2. SQL Inner Join
- 5.3. SQL Left Join
- 5.4. SQL Right Join
- 5.5. SQL Full Join
- 5.6. SQL Self Join
- 5.7. SQL Union
- 5.8. SQL Union All
---
## 6. Agrupamento e Filtragem
- 6.1. SQL Group By
- 6.2. SQL Having
---
## 7. Avançados
- 7.1. SQL Stored Procedures
- 7.2. SQL Comments

  ---

# Como Incorporar o Apache Superset em um Site Usando o SDK (Maneira Profissional)

---

A incorporação (ou *embedding*) de dashboards e gráficos do Apache Superset em páginas da web é uma funcionalidade poderosa que permite integrar suas análises em sistemas ou aplicativos externos. A maneira profissional e segura de fazer isso é utilizando o **SDK de Incorporação** do Superset.

### **Pré-requisitos**

1.  **Apache Superset instalado:** Você precisa ter uma instância do Superset rodando.
2.  **Repositório do Superset clonado:** É recomendável clonar o repositório do Superset para ter acesso aos arquivos de exemplo e às configurações.
3.  **Conhecimento básico de programação:** Familiaridade com Python, JavaScript, Flask/Node.js é útil.

---

### **Etapa 1: Habilitar a Incorporação no Superset**

1.  **Configure o Superset:** No arquivo `superset_config.py` da sua instância, adicione as seguintes linhas para habilitar a incorporação:

    ```python
    # Habilita a incorporação
    ENABLE_EMBEDDED_SUPERSET = True

    # Define o domínio permitido para a incorporação
    # Exemplo: 'http://localhost:3000' ou '[https://seu-dominio.com](https://seu-dominio.com)'
    EMBEDDED_SUPERSET_URL = 'http://localhost:3000' # Altere para o seu domínio
    ```

2.  **Reinicie o Superset:** Após alterar o arquivo de configuração, você precisa reiniciar o serviço para que as mudanças tenham efeito.

---

### **Etapa 2: Gerar um Token de Incorporação**

A incorporação segura requer que você gere um token JWT (JSON Web Token) para cada sessão de usuário. Este token irá autenticar e autorizar o usuário a visualizar o conteúdo incorporado.

1.  **Crie um script Python para gerar o token:**
    Você precisará instalar a biblioteca `PyJWT` (`pip install PyJWT`). Este script de exemplo cria um token com um payload simples.

    ```python
    import jwt
    import datetime

    # Substitua com a sua chave secreta (deve ser a mesma do superset_config.py)
    # A chave secreta pode ser gerada com `secrets.token_hex(32)`
    SECRET_KEY = 'SUA_CHAVE_SECRETA_MUITO_FORTE' 

    def create_jwt_token(user_id):
        payload = {
            'user_id': user_id,
            'exp': datetime.datetime.utcnow() + datetime.timedelta(minutes=5),  # Token expira em 5 minutos
            'iat': datetime.datetime.utcnow()
        }
        return jwt.encode(payload, SECRET_KEY, algorithm='HS256')

    # Exemplo de uso
    token = create_jwt_token('usuario_exemplo')
    print(token)
    ```

    **Importante:** A `SECRET_KEY` usada para assinar o token deve ser **a mesma** que a variável `SECRET_KEY` no seu arquivo `superset_config.py`.

---

### **Etapa 3: Criar a Página de Incorporação (Frontend)**

Agora, vamos criar a página HTML e o script JavaScript que irá carregar o dashboard ou gráfico do Superset.

1.  **Crie um arquivo HTML (`index.html`):**

    ```html
    <!DOCTYPE html>
    <html>
    <head>
        <title>Dashboard Incorporado do Superset</title>
        <style>
            iframe {
                border: none;
                width: 100%;
                height: 800px; /* Ajuste a altura conforme necessário */
            }
        </style>
    </head>
    <body>
        <h1>Meu Dashboard Superset</h1>
        <iframe id="superset-iframe"></iframe>

        <script>
            // Insira o token JWT gerado pelo seu backend aqui
            const jwtToken = 'TOKEN_JWT_GERADO_PELO_SEU_BACKEND'; 
            
            // Substitua com a URL base da sua instância do Superset e o ID do dashboard/gráfico
            const supersetUrl = 'http://localhost:8088'; 
            const dashboardId = '1'; // Altere para o ID do seu dashboard
            
            const iframe = document.getElementById('superset-iframe');
            
            iframe.src = `${supersetUrl}/embedded/dashboard/${dashboardId}?jwt=${jwtToken}`;
        </script>
    </body>
    </html>
    ```

2.  **Execute o servidor:** Crie um servidor backend (usando Flask, Express.js, etc.) que irá:
    * Gerar o token JWT (conforme a Etapa 2) para o usuário autenticado.
    * Servir a página HTML (`index.html`) para o cliente, injetando o token JWT gerado na variável `jwtToken`.

**Fluxo de funcionamento:**
1. O usuário acessa sua página (`seu-dominio.com`).
2. Seu backend gera um token JWT específico para esse usuário.
3. A página HTML é servida com o token injetado no JavaScript.
4. O JavaScript constrói a URL com o token e carrega o dashboard do Superset dentro do `<iframe>`.
5. O Superset valida o token e exibe o dashboard, garantindo que apenas usuários autorizados tenham acesso.

Este método é o mais seguro e escalável, pois o token JWT garante a autenticação e a autorização de cada sessão de incorporação.
