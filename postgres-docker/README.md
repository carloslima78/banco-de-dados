# Guia Completo: Configurando PostgreSQL e pgAdmin com Docker

## Introdução

Este guia descreve como configurar PostgreSQL e pgAdmin usando Docker. Vamos abordar desde a instalação do Docker, execução de containers usando linha de comando e Docker Compose, até operações CRUD e a remoção de containers e imagens.

## Instalação do Docker

**No Ubuntu**

1. Atualize os pacotes:

```sh
sudo apt update
```

2. Instale o Docker:

```sh
sudo apt install docker.io
```

3. Verifique a instalação do Docker:

```sh
docker --version
```

## Configurando PostgreSQL

###  Usando linha de comando

1. Baixe a imagem do PostgreSQL:

```sh
docker pull postgres
```

2. Execute o container do PostgreSQL:

```sh
docker run --name meu-postgres -e POSTGRES_PASSWORD=minha_senha -d -p 5432:5432 postgres
```

### Usando Docker Compose

1. Crie um arquivo **docker-compose.yml** com o seguinte conteúdo:

```yaml
version: '3.1'

services:
  postgres:
    image: postgres
    container_name: meu-postgres
    environment:
      POSTGRES_PASSWORD: minha_senha
    ports:
      - "5432:5432"
```

2. Inicie o container

```sh
docker-compose up -d
```

### Consultas de Database e Tabelas

Conecte-se ao PostgreSQL no container no modo interativo:

```sh
docker exec -it meu-postgres psql -U postgres
```

### Verificando databases existentes:

```sh
\l
```

### Criando uma nova database:

```sql
CREATE DATABASE produtos_db;
\c produtos_db
```

### Operações CRUD:

Crie as tabelas de exemplo fabricante e produto:

```sql
CREATE TABLE fabricante (
    id SERIAL PRIMARY KEY,
    nome TEXT NOT NULL,
    pais TEXT
);

CREATE TABLE produto (
    id SERIAL PRIMARY KEY,
    nome TEXT NOT NULL,
    fabricante_id INTEGER REFERENCES fabricante(id)
);
```

Inserindo Dados (Create)

```sql
INSERT INTO fabricante (nome, pais) VALUES ('Fabricante A', 'Brasil');
INSERT INTO fabricante (nome, pais) VALUES ('Fabricante B', 'EUA');
INSERT INTO fabricante (nome, pais) VALUES ('Fabricante C', 'Alemanha');

INSERT INTO produto (nome, fabricante_id) VALUES ('Produto 1', 2);
INSERT INTO produto (nome, fabricante_id) VALUES ('Produto 2', 2);
INSERT INTO produto (nome, fabricante_id) VALUES ('Produto 3', 3);
```

Consultando os dados inseridos:

```sql
SELECT * FROM fabricante;
SELECT * FROM produto;
SELECT * FROM produto WHERE fabricante_id = 2;
```

Atualizando os dados inseridos:

```sql
UPDATE fabricante SET nome = 'Fabricante A Modificado', pais = 'Brasil Modificado' WHERE id = 1;
UPDATE produto SET nome = 'Produto 1 Modificado', fabricante_id = 3 WHERE id = 1;
```

Excluindo os dados:

```sql
DELETE FROM fabricante WHERE id = 1;
DELETE FROM produto WHERE id = 1;
```

## Configurando pgAdmin

### Usando linha de comando

1. Baixe e inicie o container do pgAdmin. Vamos usar a porta **8081** da máquina local para evitar conflito com outras aplicações que possam estar usando a porta **8080**:

```sh
docker run --name pgadmin4 -e PGADMIN_DEFAULT_EMAIL=admin@admin.com -e PGADMIN_DEFAULT_PASSWORD=admin -d -p 8081:80 dpage/pgadmin4
```

### Usando Docker Compose

Adicione o serviço do pgAdmin ao arquivo **docker-compose**.yml:

```yml
version: '3.1'

services:
  postgres:
    image: postgres
    container_name: meu-postgres
    environment:
      POSTGRES_PASSWORD: minha_senha
    ports:
      - "5432:5432"

  pgadmin:
    image: dpage/pgadmin4
    container_name: pgadmin4
    environment:
      PGADMIN_DEFAULT_EMAIL: admin@admin.com
      PGADMIN_DEFAULT_PASSWORD: admin
    ports:
      - "8081:80"
```

Inicie o container:

```sh
docker-compose up -d
```

### Configurando a conexão no pgAdmin

1. Acesse o pgAdmin em http://localhost:8081.
2. Faça login com:
    - **Email**: admin@admin.com
    - **Password**: admin
3. Adicione o servidor PostgreSQL:
    - **Name**: PostgreSQL Docker
    - **Host name/address**: host.docker.internal (ou 172.17.0.1 no Linux)
    - **Port**: 5432
    - **Maintenance database**: postgres
    - **Username**: postgres
    - **Password**: minha_senha