# FutureVet - Deploy com Docker + MySQL

--
## 👥 Equipe

| Nome | RM |
|------|-----|
| João Victor Caetano Alves da Silva | RM 562074 |
| João Victor Bueno Castelini da Silva | RM 564115 |
| Ryan Vetoriano | RM 565667 |
| Felipe Furlanetto | RM 562766 |
| Raul Rezende Iemini Aguiar | RM 564002 |

--

## Estrutura de arquivos

```
projeto/
├── Dockerfile.api                          ← Dockerfile da API
├── Dockerfile.mysql                        ← Dockerfile do MySQL
├── pom.xml                                 ← substituir o original
├── application.properties                  ← colocar em src/main/resources/
└── docker-entrypoint-initdb.d/
    └── init.sql                            ← schema + dados iniciais
```

---

## Passo a passo

### 1. Criar volume e network

```bash
docker volume create mysql-futurevet-data

docker network create futurevet-network
```

---

### 2. Build e execução do MySQL

```bash
# Entrar na pasta do Dockerfile.mysql
docker build -f Dockerfile.mysql -t mysql-futurevet-image .

docker run --name mysql-futurevet -d \
  -p 3306:3306 \
  -v mysql-futurevet-data:/var/lib/mysql \
  --network futurevet-network \
  mysql-futurevet-image
```

**Validar o banco:**
```bash
docker logs -f mysql-futurevet

docker exec -it mysql-futurevet mysql -u user-futurevet -p
# senha: senha-futurevet

show databases;
use `db-futurevet`;
select * from tb_usuario;
```

---

### 3. Build e execução da API

```bash
# Na raiz do projeto Java
docker build -f Dockerfile.api -t api-futurevet-image .

docker run --name api-futurevet -d \
  -p 8080:8080 \
  --network futurevet-network \
  api-futurevet-image
```

**Validar a API:**
```bash
docker logs -f api-futurevet
```

Acessar:
- API:     http://localhost:8080
- Swagger: http://localhost:8080/swagger-ui/index.html

---

## Troubleshooting

```bash
# Ver containers rodando
docker ps

# Logs da API
docker logs -f api-futurevet

# Logs do MySQL
docker logs -f mysql-futurevet

# Remover containers
docker rm -f api-futurevet
docker rm -f mysql-futurevet

# Remover volume (apaga os dados!)
docker volume rm mysql-futurevet-data
```

---

## Variáveis de ambiente

| Variável                     | Valor                                              |
|------------------------------|----------------------------------------------------|
| MYSQL_ROOT_PASSWORD          | senha-futurevet                                    |
| MYSQL_DATABASE               | db-futurevet                                       |
| MYSQL_USER                   | user-futurevet                                     |
| MYSQL_PASSWORD               | senha-futurevet                                    |
| SPRING_DATASOURCE_URL        | jdbc:mysql://mysql-futurevet:3306/db-futurevet     |
| SPRING_DATASOURCE_USERNAME   | user-futurevet                                     |
| SPRING_DATASOURCE_PASSWORD   | senha-futurevet                                    |
