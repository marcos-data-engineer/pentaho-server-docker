# Pentaho Business Analytics Server 9.0 (Docker Edition)

[![Docker](https://img.shields.io/badge/Docker-Containerized-2496ED?logo=docker&logoColor=white)](https://www.docker.com/)
[![Java](https://img.shields.io/badge/Java-OpenJDK%208-ED8B00?logo=openjdk&logoColor=white)](https://openjdk.org/)
[![Pentaho](https://img.shields.io/badge/Pentaho-Server%209.0%20CE-green)](https://www.hitachivantara.com/)
[![Database Drivers](https://img.shields.io/badge/JDBC-PostgreSQL%20%7C%20Oracle%20%7C%20MySQL-blue)](#)

Implantação conteinerizada e pronta para produção do **Pentaho Business Analytics Server 9.0 (Community Edition)**. 

Este projeto empacota o ecossistema completo do Pentaho Server com suporte pré-configurado a drivers JDBC corporativos (PostgreSQL, Oracle Database), persistência em volumes locais para repositórios Jackrabbit e HSQLDB de agendamentos (Quartz), além de execução segura sob usuário não-root (`pentaho`).

---

## 🏗️ Arquitetura do Container

```mermaid
graph TD
    Client["Navegador Web / BI Consumer"] -->|Porta 8081:8080| PentahoServer["Pentaho Server 9.0 CE (Tomcat 9 / OpenJDK 8)"]

    subgraph Container["🐳 Pentaho Docker Container"]
        PentahoServer --> Engine["ETL Engine (Kettle / PDI)"]
        PentahoServer --> Quartz["Quartz Scheduler (Cron Jobs)"]
        PentahoServer --> Jackrabbit["Jackrabbit Repository (Soluções & Roles)"]
        PentahoServer --> Drivers["JDBC Drivers (Oracle, Postgres)"]
    end

    subgraph Volumes["💾 Persistência em Host (Volumes Locais)"]
        Quartz --> VolHSQL["./data/hsqldb (Metadados Quartz & Hibernate)"]
        Jackrabbit --> VolJack["./data/jackrabbit-repository (Jobs, TRs, Conexões)"]
        PentahoServer --> VolLogs["./logs (Logs do Tomcat & Pentaho)"]
    end

    subgraph ExternalDBs["🗄️ Bancos de Dados Corporativos"]
        Drivers --> Oracle["Oracle Database"]
        Drivers --> Postgres["PostgreSQL Data Warehouse"]
    end
```

---

## 🚀 Como Executar

### 1. Pré-requisitos
- Docker Engine >= 20.10
- Docker Compose >= 1.29 ou Docker Compose v2

### 2. Criar a Rede Docker e Subir o Serviço
```bash
# Criar a rede compartilhada
docker network create network_pentaho 2>/dev/null || true

# Subir o container (build da imagem local)
docker-compose up -d --build
```

### 3. Acessar a Interface Web
Acesse no seu navegador:
* **URL:** `http://localhost:8081/pentaho`
* **Usuário Padrão:** `admin`
* **Senha Padrão:** `password`

---

## ⚙️ Estrutura do Repositório

```plaintext
pentaho-server-docker/
├── data/
│   ├── hsqldb/                 # Metadados de agendamentos e hibernate
│   └── jackrabbit-repository/  # Repositório de usuários, conexões e transformações
├── lib/                        # Drivers JDBC adicionais (Oracle, PostgreSQL)
├── docker-compose.yml          # Definição dos serviços, portas e volumes
├── Dockerfile                  # Construção da imagem baseada em OpenJDK 8
└── README.md                   # Documentação técnica e operacional
```

---

## 🔒 Segurança e Boas Práticas
- **Usuário Não-Root:** O serviço executa estritamente sob o usuário de sistema `pentaho` (UID dedicado), garantindo conformidade com padrões de segurança corporativos.
- **Persistência Externa:** Nenhuma transformação (`.ktr`), job (`.kjb`) ou agendamento é perdido em reinicializações ou updates do container, graças ao isolamento dos volumes em `./data/`.

---

## 👤 Autor

**Marcos Vasconcellos de Andrade**  
*Consultor de TI & Especialista em Infraestrutura Linux, Engenharia de Dados & IA Privada*  
- **GitHub:** [@marcos-data-engineer](https://github.com/marcos-data-engineer)  
- **LinkedIn:** [Marcos Vasconcellos de Andrade](https://www.linkedin.com/in/marcos-data-engineer)  
- **Blog:** [Arquitetura de Dados & IA](https://marcos-data-engineer.github.io/blog/)  
