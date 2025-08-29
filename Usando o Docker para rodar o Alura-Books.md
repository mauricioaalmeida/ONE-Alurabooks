

crie um diretório: alurabooks e 
```
mkdir alurabooks
cd alurabooks

```

Clone o repositório da API:

```bash
# baixando o backend
git clone https://github.com/alura-cursos/api-alurabooks.git
```

Clone o repositório do Front-end:
```bash
# baixando o backend
git clone https://github.com/alura-cursos/curso-react-alurabooks.git
```

Criar o arquivo DockerFile a seguir dentro da pasta api-alurabooks

```
cd api-alurabooks
notepad DockerFile
```


```
# Use uma imagem base do Node.js mais enxuta (melhor para produção)
FROM node:18-alpine

# Define o diretório de trabalho dentro do container
WORKDIR /app

# Copia o package.json e o package-lock.json para o diretório de trabalho
COPY package*.json ./

# Instala as dependências de produção. 'npm ci' é mais rápido e seguro para builds.
RUN npm ci --only=production

# Copia o restante dos arquivos da aplicação.
# É uma boa prática ter um .dockerignore para não copiar node_modules, .git, etc.
COPY . .

# A aplicação original roda na porta 8000
EXPOSE 8000

# Define o comando que será executado ao iniciar o container
CMD ["npm", "run", "start-auth"]
```

Criar o arquivo .dockerignore a seguir dentro da pasta api-alurabooks

```
notepad .dockerignore
```

```
.git
.gitignore
node_modules
Dockerfile
README.md
```

Criar o arquivo DockerFile a seguir dentro da pasta curso-react-alurabooks

```
cd curso-react-alurabooks
notepad DockerFile
```

```
# Use uma imagem base do Node.js
FROM node:18-alpine

# Define o diretório de trabalho
WORKDIR /app

# Copia os arquivos de dependência
COPY package*.json ./

# Instala as dependências
RUN npm install

# Copia o restante do código
COPY . .

# Expõe a porta do servidor de desenvolvimento do React
EXPOSE 3000

# Comando para iniciar o servidor de desenvolvimento
CMD ["npm", "start"]
```

Criar o arquivo .dockerignore a seguir dentro da pasta curso-react-alurabooks
```
notepad .dockerignore
```

```
.git
.gitignore
node_modules
Dockerfile
README.md
build
```



criar o arquivo docker-compose.yml na pasta alurabooks
```
cd ..
notepad docker-compose.yml
```

```
services:
  api:
    build: ./api-alurabooks
    container_name: alurabooks-api
    ports:
      - "8000:8000"
    volumes:
      # Monta o código para refletir alterações em tempo real (desenvolvimento)
      - ./api-alurabooks:/app
      # Evita que o node_modules local sobrescreva o do container
      - /app/node_modules
    networks:
      - alurabooks-net

  frontend:
    build: ./curso-react-alurabooks
    container_name: alurabooks-frontend
    ports:
      - "3000:3000"
    volumes:
      - ./curso-react-alurabooks:/app
      - /app/node_modules
    depends_on:
      - api
    networks:
      - alurabooks-net
    environment:
      - CHOKIDAR_USEPOLLING=true

networks:
  alurabooks-net:
    driver: bridge

```


Crie os contêiner e inicie a aplicação com:
```
docker-compose up --build
```

Acesse a aplicação em http://localhost:3000