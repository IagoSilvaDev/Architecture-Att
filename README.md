---

# TI Soluções Incríveis - Projeto de Migração Fast Engineering S/A

![CloudPress Compass](https://vetores.org/d/compass-uol.svg)

---

## 1. Contexto

A **Fast Engineering S/A** está migrando sua infraestrutura de eCommerce para a AWS devido a limitações de escalabilidade e desempenho.  
A infraestrutura on-premise atual inclui:

- **Banco de Dados:** 1 servidor MySQL (500GB de dados, 10GB RAM, 3 Core CPU)
- **Frontend:** 1 servidor React (5GB armazenamento, 2GB RAM, 1 Core CPU)
- **Backend:** 1 servidor com 3 APIs, Nginx (balanceador de carga), 5GB armazenamento, 4GB RAM, 2 Core CPU

**Estratégia de Migração:**
1. **Etapa 1 (Lift-and-Shift):** Replicação "As-Is" na AWS com mínimas alterações.
2. **Etapa 2 (Modernização):** Adoção de Kubernetes (EKS), CI/CD e autoscaling.

---

## 2. Etapa 1: Lift-and-Shift (As-Is)

### 2.1 Arquitetura AWS

![Diagrama da Infraestrutura](./imgs/AWS_Migration.drawio.png)

### 2.2 Componentes e Funcionamento

#### Rede e Segurança

- **VPC (Virtual Private Cloud)**
  - **Sub-redes Públicas:**
    - Hospedam o NAT Gateway e o Application Load Balancer (ALB)
    - Distribuídas em 2 AZs para alta disponibilidade
  - **Sub-redes Privadas:**
    - Isolam instâncias EC2 do Backend e o Banco de Dados RDS
  - **Network ACL (NACL):**
    - Regras restritivas permitindo apenas tráfego HTTPS (443) e SSH (22)
  - **WAF (Web Application Firewall):**
    - Protege o ALB contra ataques (ex.: SQL Injection, XSS)

- **Conectividade Híbrida**
  - **VPN:** Garante comunicação segura entre o ambiente on-premise e a AWS durante a migração

#### Computação e Armazenamento

- **Amazon EC2**
  - **Frontend:** Instância `t3.small` (2 vCPU, 2GB RAM) executando React; Armazenamento: Volume EBS gp2 de 10GB
  - **Backend:** Instância `t3.medium` (2 vCPU, 4GB RAM) executando APIs, com balanceamento via ALB

- **Amazon RDS (MySQL)**
  - Instância `db.t3.medium` (2 vCPU, 4GB RAM) com configuração Multi-AZ e backups diários (retenção de 7 dias)

- **Amazon S3**
  - Bucket `fast-engineering-static`: Armazena imagens, vídeos e logs, com versionamento habilitado

#### Migração de Dados

- **AWS DMS (Database Migration Service)**
  - Replica dados do MySQL on-premise para o RDS, utilizando uma instância `dms.t3.medium` e validação via checksums

- **AWS Application Migration Service**
  - Replica os servidores Frontend e Backend para instâncias EC2 por meio de snapshot contínuo

#### Monitoramento e Gestão

- **Amazon CloudWatch**
  - Coleta métricas (CPU, RAM, tráfego de rede) das instâncias EC2 e RDS
  - Configura alertas para: utilização de CPU > 80% e latência do ALB > 500ms

- **AWS IAM**
  - Define políticas de acesso mínimo:
    - EC2: Apenas leitura em S3
    - RDS: Acesso restrito (somente via APIs)

### 2.3 Ferramentas e Serviços

#### Tabela de Ferramentas e Serviços

| Ícone | Serviço | Descrição |
|:-----:|---------|-----------|
| <img src="https://cloud-icons.onemodel.app/aws/Architecture-Service-Icons_01312023/Arch_Migration-Transfer/64/Arch_AWS-Application-Migration-Service_64@5x.png" width="50" alt="AWS Application Migration Service"> | **AWS Application Migration Service** | Automatiza a migração de aplicações on-premises para a AWS, simplificando o processo e minimizando o tempo de inatividade. |
| <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRULf2JOHbvkPux8pEzQrkH70TVSpfgRMzgQA&s" width="50" alt="Amazon EC2"> | **Amazon EC2** | Instâncias virtuais que hospedam as aplicações migradas, proporcionando escalabilidade e flexibilidade operacional. |
| <img src="https://cdn.worldvectorlogo.com/logos/amazon-s3-simple-storage-service.svg" width="50" alt="Amazon S3"> | **Amazon S3** | Serviço de armazenamento de objetos escalável para backups, armazenamento temporário e arquivamento de dados. |
| <img src="https://cdn.worldvectorlogo.com/logos/amazon-elastic-block-store-1.svg" width="50" alt="Amazon EBS"> | **Amazon EBS** | Armazenamento em bloco para EC2, garantindo replicação fiel dos volumes de dados. |
| <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQ7L7fI-Ozxh2ni9T2E7rgX_CU-VNMOpoXfwpIxYIaifUcJL_NQ0ZJi8mGHWNRdiFXmres&usqp=CAU" width="50" alt="Amazon RDS"> | **Amazon RDS** | Banco de dados relacional gerenciado para MySQL, com alta disponibilidade e suporte Multi-AZ. |
| <img src="https://cloud-icons.onemodel.app/aws/Architecture-Service-Icons_01312023/Arch_Database/64/Arch_AWS-Database-Migration-Service_64@5x.png" width="50" alt="AWS DMS"> | **AWS Database Migration Service (DMS)** | Facilita a migração de bancos de dados com replicação contínua e mínima interrupção, garantindo a integridade dos dados. |
| <img src="https://github.com/user-attachments/assets/74224709-c040-46dd-858c-6307886fb90d" width="50" alt="Amazon VPC"> | **Amazon VPC** | Proporciona isolamento e segmentação da rede na AWS, permitindo a criação de sub-redes, endpoints e controle de tráfego. |
| <img src="https://cloud-icons.onemodel.app/aws/Architecture-Service-Icons_01312023/Arch_Security-Identity-Compliance/64/Arch_AWS-Identity-and-Access-Management_64.png" width="50" alt="AWS IAM"> | **AWS IAM** | Gerencia acesso e permissões de usuários e serviços, assegurando que somente operações autorizadas sejam realizadas. |
| <img src="https://cloud-icons.onemodel.app/aws/Architecture-Service-Icons_01312023/Arch_Security-Identity-Compliance/64/Arch_AWS-Key-Management-Service_64.png" width="50" alt="AWS KMS"> | **AWS Key Management Service (KMS)** | Gerencia e protege chaves de criptografia, assegurando a proteção dos dados em trânsito e em repouso. |
| <img src="https://icon.icepanel.io/AWS/svg/Networking-Content-Delivery/Client-VPN.svg" width="50" alt="AWS VPN"> | **AWS VPN** | Estabelece conexões seguras entre o ambiente on-premise e a AWS, garantindo a transferência segura dos dados. |
| <img src="https://cloud-icons.onemodel.app/aws/Architecture-Service-Icons_01312023/Arch_Management-Governance/64/Arch_Amazon-CloudWatch_64.png" width="50" alt="Amazon CloudWatch"> | **Amazon CloudWatch** | Oferece monitoramento e observabilidade com coleta de métricas, logs e eventos para acompanhar a performance e integridade dos recursos. |

---

## 3. Etapa 2: Modernização (EKS e CI/CD)

### 3.1 Arquitetura AWS

![Diagrama Modernização](imgs/Modern_architecture_eks.drawio.png)

### 3.2 Componentes e Funcionamento

#### Kubernetes (EKS)

- **EKS Cluster**
  - **Namespaces:**
    - `frontend`: Implantação de containers React via **Deployment** com 3 réplicas
    - `backend`: Execução de APIs em containers com autoscaling (2-10 pods)
  - **Ingress Controller:**
    - Roteia tráfego via ALB (HTTP/HTTPS) com anotações para integração com WAF

- **Auto Scaling**
  - **Horizontal Pod Autoscaler (HPA):** Escala pods baseado na utilização de CPU (meta: 70%)
  - **Cluster Autoscaler:** Ajusta o número de nós do cluster conforme a demanda

#### CI/CD e Registro de Containers

- **AWS CodePipeline**
  - Fluxo: **CodeCommit** (repositório) → **CodeBuild** (build da imagem Docker) → **ECR** (registro) → **EKS** (deploy)
  - Gatilho: Push para a branch `main`

- **Amazon ECR**
  - Repositório privado para imagens Docker

#### Monitoramento Avançado

- **Amazon CloudWatch**
  - Coleta métricas customizadas do EKS (ex.: número de pods, latência de resposta)
  - Dashboards para visualização em tempo real

### 3.3 Ferramentas e Serviços

#### Tabela de Ferramentas e Serviços (Modernização)

| Ícone | Serviço | Descrição |
|:-----:|---------|-----------|
| <img src="https://d1.awsstatic.com/icons/jp/console_eks_icon.67a5d60f7b4fc9b6b0ccb1e3c5aeabc5f8b8d4a7.png" width="50" alt="Amazon EKS"> | **Amazon EKS** | Orquestração de containers para Frontend e Backend. |
| <img src="https://d1.awsstatic.com/icons/jp/console_codepipeline_icon.67a5d60f7b4fc9b6b0ccb1e3c5aeabc5f8b8d4a7.png" width="50" alt="AWS CodePipeline"> | **AWS CodePipeline** | Pipeline de CI/CD que automatiza os deployments. |
| <img src="https://d1.awsstatic.com/icons/jp/console_ecr_icon.67a5d60f7b4fc9b6b0ccb1e3c5aeabc5f8b8d4a7.png" width="50" alt="Amazon ECR"> | **Amazon ECR** | Registro de imagens Docker com escaneamento de segurança. |
| <img src="https://d1.awsstatic.com/icons/jp/console_route53_icon.67a5d60f7b4fc9b6b0ccb1e3c5aeabc5f8b8d4a7.png" width="50" alt="Route 53"> | **Route 53** | Gerencia DNS e roteamento para o ALB. |
| <img src="https://cloud-icons.onemodel.app/aws/Architecture-Service-Icons_01312023/Arch_Management-Governance/64/Arch_Amazon-CloudWatch_64.png" width="50" alt="Amazon CloudWatch"> | **Amazon CloudWatch** | Monitoramento centralizado de métricas e logs. |

---

## 4. Requisitos de Segurança

### Criptografia

- **Em Trânsito:** TLS 1.3 para comunicação entre ALB, EKS e RDS  
- **Em Repouso:**
  - **EBS:** Criptografia AES-256 via AWS KMS  
  - **S3:** Server-Side Encryption (SSE-S3)

### Controle de Acesso

- **IAM Roles:**
  - **EC2:** Permissão apenas para pull de imagens do ECR  
  - **EKS:** Políticas de acesso restrito aos namespaces
- **Secret Manager:**  
  - Armazena credenciais do RDS e chaves de API

### Backup e Recuperação

- **RDS:** Snapshots automáticos diários  
- **S3:** Versionamento e lifecycle policies para arquivos estáticos  
- **EKS:** Backup de configurações via Velero

---

