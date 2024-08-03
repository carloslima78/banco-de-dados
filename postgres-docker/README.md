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

## Consultas de Database e Tabelas

Conecte-se ao PostgreSQL no container no modo interativo:

```sh
docker exec -it meu-postgres psql -U postgres
```