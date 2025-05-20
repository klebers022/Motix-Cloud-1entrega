# ☁️ MOTIX - DevOps Tools & Cloud Computing

Este repositório faz parte do módulo **DevOps Tools & Cloud Computing** e demonstra a **containerização**, **deploy em nuvem (Azure)** e o uso de ferramentas essenciais como **Docker**, **Azure CLI** e **VMs Linux**.

A aplicação utilizada é a **API de Usuários da Motix**, desenvolvida em **Java com Spring Boot**.

---

## 📌 Descrição do Projeto

Este projeto implementa:
- Containerização da aplicação Java com Docker.
- Upload da imagem para o DockerHub.
- Criação e configuração de uma VM Linux no Microsoft Azure.
- Deploy da aplicação Java em um container exposto na porta 8080.
- Criação de regras de segurança para liberação da aplicação externamente.

---

## 🌐 Rotas da API

| Método | Endpoint                      | Descrição                       |
|--------|-------------------------------|----------------------------------|
| GET    | `/users`                      | Lista todos os usuários          |
| GET    | `/users/{id}`                 | Busca usuário por ID             |
| GET    | `/users/rm/{rm}`              | Busca usuário por RM             |
| GET    | `/users/{name}`               | Busca usuários por nome          |
| POST   | `/users`                      | Cadastra um novo usuário         |
| PUT    | `/users`                      | Atualiza um usuário              |
| DELETE | `/users/{id}`                 | Remove usuário por ID            |
| DELETE | `/users/rm/{rm}`              | Remove usuário por RM            |

---

## ⚙️ Instalação Local

### Pré-requisitos

- Java 17
- Maven
- Docker instalado

### Passos

```bash
# 1. Clone o repositório
git clone https://github.com/motix.git
cd motix
cd java

# 2. Build do projeto
mvn clean package -DskipTests

# 3. Build do container
docker build -t motix:latest .

# 4. Rodar o container localmente
docker run -d -p 8080:8080 motix:latest
```

---

## 🐳 Dockerfile

```Dockerfile
FROM maven:3.8.5-openjdk-17 AS builder

WORKDIR /app

COPY pom.xml .
COPY src ./src

RUN mvn clean package -DskipTests

FROM eclipse-temurin:17-jdk-alpine

WORKDIR /app

COPY --from=builder /app/target/*.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]
```

---

## ☁️ Deploy na Azure (VM + Docker)

### 🔷 1. Criar grupo de recursos
```bash
az group create -l eastus -n rg-vm-challenge
```

### 🔷 2. Criar VM Linux Ubuntu
```bash
az vm create   --resource-group rg-vm-challenge   --name vm-challenge   --image Canonical:ubuntu-24_04-lts:minimal:24.04.202505020   --size Standard_B2s   --admin-username admin_fiap   --admin-password admin_fiap@123
```

### 🔷 3. Instalar Docker na VM
Acessar a VM via SSH e instalar Docker:
```bash
sudo apt update
sudo apt install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
```

### 🔷 3.5. Login no DockerHub
```bash
docker login -u seu usuario
```

### 🔷 4. Rodar container com imagem publicada
```bash
docker run -d -p 8080:8080 lucasrainha/motix:0.2
```

### 🔷 5. Abrir porta 8080 no NSG da VM
```bash
az network nsg rule create   --resource-group rg-vm-challenge   --nsg-name vm-challengeNSG   --name port_8080   --protocol tcp   --priority 1010   --destination-port-range 8080
```

### 🔷 6. (Opcional) Liberar porta 80
```bash
az network nsg rule create   --resource-group rg-vm-challenge   --nsg-name vm-challengeNSG   --name port_80   --protocol tcp   --priority 1020   --destination-port-range 80
```

---

## 👥 Participantes

- Kleber da Silva — RM: 557887  
- Nicolas Barutti — RM: 554944  
- Lucas Rainha — RM: 558471

---

