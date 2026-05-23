# Projeto de Conscientização Prática

Laboratório de capacitação em segurança da informação utilizando CTFd e OWASP Juice Shop executando em containers Docker.

O projeto foi criado para permitir que qualquer usuário consiga:

- subir rapidamente um ambiente de treinamento;
- executar apenas os serviços desejados;
- replicar facilmente o laboratório;
- utilizar em treinamentos hands-on internos, workshops e competições entre equipes.

### Infraestrutra compatível:

- Localmente (`localhost`)
- VPS (*Virtual Private Server*)
- Cloud

### O ambiente possui 3 componentes principais:

| Serviço          | Função                                    |
| ---------------- | ----------------------------------------- |
| CTFd             | Plataforma de gerenciamento de desafios   |
| PostgreSQL       | Banco de dados do CTFd                    |
| OWASP Juice Shop | Aplicação web vulnerável para treinamento |

#### O projeto foi estruturado para permitir diferentes cenários

| Modo  | Serviços Executados            |
| ----- | ------------------------------ |
| full  | CTFd + PostgreSQL + Juice Shop |
| ctfd  | Apenas CTFd + PostgreSQL       |
| juice | Apenas Juice Shop              |


### Arquitetura do Ambiente 

                    |<   Docker Network  >|
                    |                     |  
                    |                     |
    |===============|                     |===============|  
    |      CTFd     |                     |   Juice Shop  |
    |===============|                     |===============|   
            |                                   
    |===============|
    |   PostgreSQL  |                                           
    |===============|

## Pré-requisitos 
- Docker 24+
- Docker-compose
- Linux/macOS/WSL2
- 4 GB RAM mínimos
- 2 vCPU recomendados


---
```bash
sudo apt update

sudo apt install -y docker.io docker-compose-plugin

sudo systemctl enable docker
sudo systemctl start docker
```
## instalação
```bash
git clone https://github.com/Enzofmlg/projeto-de-conscientizacao.git

cd projeto-de-conscientizacao
```

---
## Validando instalação

```bash
docker --version

docker compose version
```

Se ambos retornarem versão, o ambiente está pronto.

---

## Arquivo docker-compose.yml

Crie o arquivo:

```yaml
services:

  # =========================
  # CTFd
  # =========================
  ctfd:
    image: ctfd/ctfd:latest
    container_name: ctfd
    restart: unless-stopped

    ports:
      - "8000:8000"

    environment:
      DATABASE_URL: postgresql://ctfd:ctfd@db/ctfd
      SECRET_KEY: lab
      UPLOAD_FOLDER: /var/uploads
      LOG_FOLDER: /var/log/CTFd

    volumes:
      - ./ctfd/data/uploads:/var/uploads
      - ./ctfd/data/logs:/var/log/CTFd

    depends_on:
      - db

    networks:
      - labnet

    profiles:
      - ctfd
      - full

  # =========================
  # Banco de Dados
  # =========================
  db:
    image: postgres:15
    container_name: ctfd-db
    restart: unless-stopped

    environment:
      POSTGRES_DB: ctfd
      POSTGRES_USER: ctfd
      POSTGRES_PASSWORD: ctfd

    volumes:
      - postgres_data:/var/lib/postgresql/data

    networks:
      - labnet

    profiles:
      - ctfd
      - full

  # =========================
  # Juice Shop
  # =========================
  juice-shop:
    image: bkimminich/juice-shop:latest
    container_name: juice-shop
    restart: unless-stopped

    ports:
      - "3000:3000"

    networks:
      - labnet

    profiles:
      - juice
      - full

networks:
  labnet:

volumes:
  postgres_data:
```

---

# Subindo o Ambiente

## 1. Subir Ambiente Completo

Executa:
- CTFd
- PostgreSQL
- Juice Shop

```bash
docker compose --profile full up -d
```

---

## 2. Subir Apenas o CTFd

Executa:
- CTFd
- PostgreSQL

```bash
docker compose --profile ctfd up -d
```
## 3. Subir Apenas o CTFd Juice Shop

Executa:
- Juice

```bash
docker compose --profile juice up -d
```
# Referências Oficiais

#### Docker

https://docs.docker.com/


#### Docker Compose

https://docs.docker.com/compose/

---

#### CTFd

https://ctfd.io/

https://github.com/CTFd/CTFd

---

#### OWASP Juice Shop

https://owasp.org/www-project-juice-shop/

https://github.com/juice-shop/juice-shop

---

#### PostgreSQL

https://www.postgresql.org/docs/









