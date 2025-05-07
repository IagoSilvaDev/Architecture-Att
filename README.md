# TI Soluções Incríveis - Projeto de Migração Fast Engineering S/A

![CloudPress Compass](https://vetores.org/d/compass-uol.svg)


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

### 2.1 Arquitetura de Migração




  ![Diagrama da Infraestrutura](imgs/DIagram_Migration.png)



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

# Orçamento de Migração AWS - Resumo



| Serviço AWS                          | Custo Mensal (USD) | Descrição/Configuração                                                                 |
|--------------------------------------|--------------------|---------------------------------------------------------------------------------------|
| **AWS Application Migration Service** | 0,00               | 2 servidores, 6 horas/servidor.                                                      |
| **Amazon EC2 (MGN Replication)**     | 23,79              | 3 instâncias t3.medium (Linux), 40h/semana, 30 GB EBS, 18 TB entrada.                |
| **Amazon EC2 (Instâncias de Teste)** | 9,46               | 1 instância t3.small (Linux), 60% uso/mês, 5 GB EBS, 1 GB entrada/saída.             |
| **Amazon EC2 (Instância Teste 2)**   | 16,82              | 1 instância t3.medium (Linux), 60% uso/mês, 5 GB EBS, 1 GB entrada/saída.            |
| **Amazon EBS (Snapshots MGN)**       | 0,40               | 2 volumes (6 GB cada), snapshots diários, 100 MB alterados/snapshot.                 |
| **AWS DMS**                          | 164,80             | 1 instância r5.large (Single-AZ), 100 GB SSD gp2.                                    |
| **AWS KMS**                          | 3,26               | 3 chaves CMK, 85K solicitações simétricas.                                           |
| **Amazon VPC (Staging Area)**        | 49,23              | 1 VPN Site-to-Site, 18 GB entrada/mês, 1 NAT Gateway.                                |
| **Amazon VPC (Recursos Migrados)**   | 33,30              | 1 NAT Gateway, 2 GB entrada/mês.                                                     |
| **Amazon S3 (Migração)**             | 0,15               | 5 GB armazenamento (INT), 10K solicitações PUT, 3 GB entrada.                        |
| **Amazon CloudWatch**                | 9,20               | 10 GB logs, 5 métricas, 3 painéis, monitoramento DMS/MGN.                            |
| **Amazon RDS for MySQL**             | 190,65             | 1 instância db.m6g.xlarge (Single-AZ), 500 GB SSD gp2, 60% uso/mês.                  |

### Totais
- **Custo Mensal Total:** 501,06 USD  
- **Custo Anual (12 meses):** 6.012,72 USD  

[Orçamento da Migração](https://calculator.aws/#/estimate?id=a130532c90c080f1ed92fa0ffc18c16b2a94db56)

## Arquitetura Lift-and-Shift

   ![Diagrama Modernização](imgs/Diagram-Lift-and-Shift.png)

# Orçamento Pós-Migração AWS - Resumo  

| Serviço AWS                  | Custo Mensal (USD) | Descrição/Configuração                                                                 |
|------------------------------|--------------------|---------------------------------------------------------------------------------------|
| **Amazon EC2 (Frontend)**     | 8,49               | 1 instância t3a.small (Linux), 60% uso/mês, 5 GB EBS, 1 GB tráfego entrada/saída.     |
| **Amazon EC2 (Backend)**      | 16,97              | 1 instância t3a.medium (Linux), 60% uso/mês, 10 GB EBS, 3 GB saída.                   |
| **Amazon RDS for MySQL**      | 190,65             | 1 instância db.m6g.xlarge (Single-AZ), 500 GB SSD gp3, 12K IOPS, 500 MiBps throughput.|
| **AWS KMS**                   | 3,30               | 3 chaves CMK, 100K solicitações simétricas.                                           |
| **Amazon CloudWatch**         | 6,28               | 5.5 GB logs, 2 painéis, 15 métricas de alarme.                                        |
| **Amazon VPC**                | 71,29              | 1 NAT Gateway, 118 GB entrada, 100 GB saída, 1 TB tráfego intrarregião.               |

### Totais  
- **Custo Mensal Total:** 296,98 USD  
- **Custo Anual (12 meses):** 3.563,76 USD  

[Orçamento da Pós-Migração](https://calculator.aws/#/estimate?id=bfcf48e230398f658823a4afa4690de4c9b81624)

---

## 3. Etapa 2: Modernização 

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
| <img src="https://icon.icepanel.io/AWS/svg/Containers/EKS-Cloud.svg" width="50" alt="Amazon EKS"> | **Amazon EKS** | Orquestração de containers para Frontend e Backend. |
| <img src="https://icon.icepanel.io/AWS/svg/Developer-Tools/CodePipeline.svg" width="50" alt="AWS CodePipeline"> | **AWS CodePipeline** | Pipeline de CI/CD que automatiza os deployments. |
| <img src="https://icon.icepanel.io/AWS/svg/Containers/Elastic-Container-Registry.svg" width="50" alt="Amazon ECR"> | **Amazon ECR** | Registro de imagens Docker com escaneamento de segurança. |
| <img src="https://icon.icepanel.io/AWS/svg/Networking-Content-Delivery/Route-53.svg" width="50" alt="Route 53"> | **Route 53** | Gerencia DNS e roteamento para o ALB. |
| <img src="https://cloud-icons.onemodel.app/aws/Architecture-Service-Icons_01312023/Arch_Management-Governance/64/Arch_Amazon-CloudWatch_64.png" width="50" alt="Amazon CloudWatch"> | **Amazon CloudWatch** | Monitoramento centralizado de métricas e logs. |
| <img src="https://icon.icepanel.io/AWS/svg/Storage/Backup.svg" width="50" alt="AWS Backup"> | **Amazon Backup** | Automatização de backups para EC2, EBS e RDS. |

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
- **EKS:** Backup de configurações
- **Amazon Backup:** Automatização de backups para EC2, EBS e RDS.

---
  ![Diagrama Modernização](imgs/Diagram_Modern_Architeture.png)

## 4. Ambientes

### 4.1 Ambiente de Desenvolvimento (Dev/Test)
  ![Dev_Test](imgs/Ambiente_Dev_Test.png)
  
- **Recursos:**
  - Cluster EKS em uma Organizational Unit (Dev/Test OU) separada.
  - Deploys automáticos via GitHub Actions + Terraform para instâncias ou namespaces isolados.
  - Banco de dados RDS (single‑AZ) com dados de teste.
  - Bucket S3 para artefatos temporários e logs.
- **Políticas de Segurança:**
  - IAM roles de acesso restrito (privilégio mínimo).
  - Network ACLs mais permissivos para facilitar testes internos.

### 4.2 Ambiente de Homologação (Staging)
![Homologação](imgs/Ambiente_Homologação.png)

- **Recursos:**
  - Várias Availability Zones (AZs) para testar alta disponibilidade.
  - Infraestrutura quase idêntica à de produção:
    - Deploy de frontend e backend em namespaces `frontend` e `backend` no EKS.
    - Serviços e ingress via ALB + WAF.
    - RDS MySQL com Multi‑AZ.
    - Bucket S3 com versionamento ativado.
- **Políticas de Segurança:**
  - Network ACLs restritivos (apenas 443/HTTPS e 22/SSH de VPN).
  - WAF ativo no ALB.
  - Uso de Secret Manager e KMS para gerenciamento de segredos.

### 4.3 Ambiente de Produção (Prod)
![Produção](imgs/Ambiente_Produção.png)

- **Recursos:**
  - Organização em OU (Prod OU) dentro do AWS Organizations.
  - VPC distribuída em 2 AZs com sub‑redes públicas e privadas.
  - ALB + WAF + Route 53 redundante.
  - EKS Cluster dedicado com namespaces de frontend e backend.
  - Auto Scaling:
    - HPA (min:3, max:10 pods para backend).
    - Cluster Autoscaler para ajustar nós conforme demanda.
  - RDS MySQL Multi‑AZ com replicação cross‑AZ e backups contínuos.
  - Replicação de dados de imagem/log para bucket S3 e serviço de Backup dedicado.
- **Políticas de Segurança:**
  - Network ACLs e Security Groups restritivos.
  - IAM Roles e Policies de privilégio mínimo.
  - Secret Manager, KMS e GuardDuty ativos.
  - Monitoramento avançado com CloudWatch, Trusted Advisor e Config.


# 📌 Quadro do Projeto: Migração Fast Engineering S/A para AWS



   ![Projeto](imgs/projeto.png)


## 📋 Listas (Fases do Projeto)
- [Planejamento Inicial](#planejamento-inicial)
- [Etapa 1: Lift-and-Shift (As-Is)](#etapa-1-lift-and-shift-as-is)
- [Etapa 2: Modernização (EKS, CI/CD, Autoscaling)](#etapa-2-modernização-eks-cicd-autoscaling)
- [Segurança & Compliance](#segurança--compliance)
- [Orçamento & Custos](#orçamento--custos)
- [Pós-Migração & Otimização](#pós-migração--otimização)

---

### 📝 Planejamento Inicial
<details>
<summary>📌 Definir Escopo</summary>
- Validar requisitos de escalabilidade e desempenho.
- Mapear arquitetura on-premise atual.
- Definir metas de SLA pós-migração.
</details>

<details>
<summary>📌 Equipe & Responsáveis</summary>
- Atribuir papéis (Engenheiros de DevOps, Arquitetos, Segurança).
- Agendar reuniões de alinhamento semanais.
</details>

<details>
<summary>📌 Documentação</summary>
- Criar diagramas de arquitetura (VPC, EC2, RDS, etc.).
- Registrar políticas de segurança e backup.
</details>

---

### 🔧 Etapa 1: Lift-and-Shift (As-Is)
<details>
<summary>🔧 Infraestrutura na AWS</summary>
- Configurar VPC com sub-redes públicas/privadas (2 AZs).
- Implementar NAT Gateway e Application Load Balancer (ALB).
- Configurar Network ACL e WAF para proteção contra ataques.
</details>

<details>
<summary>🖥️ Migração de Servidores</summary>
- Usar AWS Application Migration Service para replicar Frontend (t3.small) e Backend (t3.medium).
- Validar configurações de armazenamento (EBS, S3).
</details>

<details>
<summary>🗄️ Migração de Dados</summary>
- Configurar AWS DMS para replicar MySQL para RDS (db.t3.medium).
- Validar integridade dos dados com checksums.
</details>

<details>
<summary>📊 Monitoramento</summary>
- Configurar CloudWatch para métricas de CPU, RAM e latência.
- Criar alertas para thresholds críticos (CPU >80%, latência ALB >500ms).
</details>

---

### 🧱 Etapa 2: Modernização (EKS, CI/CD, Autoscaling)
<details>
<summary>🧱 Kubernetes (EKS)</summary>
- Criar cluster EKS com namespaces frontend (3 réplicas) e backend (autoscaling de 2-10 pods).
- Configurar Ingress Controller com ALB e integração ao WAF.
</details>

<details>
<summary>🚀 CI/CD Pipeline</summary>
- Implementar CodePipeline: CodeCommit → CodeBuild → ECR → EKS.
- Configurar gatilho automático para pushes na branch `main`.
</details>

<details>
<summary>📈 Autoscaling</summary>
- Configurar Horizontal Pod Autoscaler (HPA) para uso de CPU (meta: 70%).
- Implementar Cluster Autoscaler para ajuste dinâmico de nós.
</details>

<details>
<summary>🛡️ Segurança Modernizada</summary>
- Integrar Secrets Manager para gerenciar credenciais do RDS.
- Atualizar criptografia em trânsito (TLS 1.3) e repouso (KMS).
</details>

---

### 🔒 Segurança & Compliance
<details>
<summary>🔒 Criptografia</summary>
- Validar TLS 1.3 em ALB, EKS e RDS.
- Ativar criptografia AES-256 para EBS e S3.
</details>

<details>
<summary>🔐 Controle de Acesso</summary>
- Configurar IAM Roles para EC2 (permissões mínimas).
- Restringir acesso ao RDS apenas via APIs.
</details>

<details>
<summary>🔄 Backup & Recuperação</summary>
- Configurar backups automáticos do RDS (7 dias de retenção).
- Ativar versionamento no S3 e lifecycle policies.
- Implementar AWS Backup para EC2, EBS e RDS.
</details>

---

### 💰 Orçamento & Custos
<details>
<summary>💰 Custos Estimados</summary>
- Comparar orçamentos: Lift-and-Shift ($501/mês) vs. Modernização ($2.337/mês).
- Monitorar custos com Cost Explorer e Budgets.
</details>

<details>
<summary>📉 Otimização de Custos</summary>
- Revisar instâncias EC2 para ajustar tamanho (ex.: migrar para t3a).
- Avaliar uso de Spot Instances para ambientes de teste.
</details>

---

### ✅ Pós-Migração & Otimização
<details>
<summary>✅ Validação Final</summary>
- Testar carga e desempenho sob alta demanda.
- Validar failover Multi-AZ no RDS e EKS.
</details>

<details>
<summary>📋 Documentação Final</summary>
- Atualizar diagramas de arquitetura pós-modernização.
- Registrar procedimentos de manutenção e disaster recovery.
</details>

<details>
<summary>📈 Melhorias Contínuas</summary>
- Implementar análises de logs com CloudWatch Logs Insights.
- Explorar uso de Lambda para automações event-driven.
</details>

# Orçamento AWS - Resumo

| Serviço AWS                  | Custo Mensal (USD) | Descrição/Configuração                                                                 |
|------------------------------|--------------------|---------------------------------------------------------------------------------------|
| **Amazon EKS**               | 876,00             | 2 clusters EKS, 0 nós híbridos/mês.                                                  |
| **Amazon CloudWatch**        | 6,28               | 5.5 GB logs consumidos, 2 painéis, 15 métricas de alarme.                            |
| **Amazon VPC**               | 105,80             | 118 GB entrada, 100 GB saída, 1 TB intrarregião, 2 NAT gateways.                     |
| **Amazon RDS for MySQL**     | 467,09             | 1 instância db.m5.xlarge (Multi-AZ), 500 GB SSD gp2, 60% utilização.                 |
| **Amazon Route 53**          | 287,70             | 2 zonas hospedadas, 500 registros, 500 domínios.                                     |
| **AWS CodePipeline**         | 0,30               | 1 pipeline V1, 250 minutos execução V2/mês.                                          |
| **AWS CodeBuild**            | 0,90               | 1 compilação/mês (Linux, arm1.2xlarge).                                              |
| **AWS Backup**               | -                  | 50 GB dados primários, retenção variável (35 dias a 3 meses).                        |
| **Amazon S3**                | 22,65              | 200 GB armazenamento, 5K solicitações PUT, 10K solicitações GET.                     |
| **AWS WAF**                  | 9,60               | 1 ACL web, 3 regras personalizadas, 1 grupo gerenciado.                              |
| **AWS KMS**                  | 11,00              | 5 chaves CMK, 2M solicitações simétricas.                                            |
| **Amazon ECR**               | 461,20             | 2 TB entrada, 5 TB saída, 4 GB armazenamento.                                        |
| **Elastic Load Balancing**   | 23,44              | 1 Application Load Balancer.                                                         |
| **AWS Secrets Manager**      | 0,41               | 1 segredo, 30 dias retenção, 1K chamadas API/mês.                                    |
| **Amazon EC2**               | 60,15              | 8 instâncias t3.small (Linux, compartilhadas), carga consistente.                    |

### Totais
- **Custo Inicial Total:** 0,00 USD  
- **Custo Mensal Total:** 2.337,64 USD *(ajustado após remoção do CodeDeploy)*  
- **Custo Anual (12 meses):** 28.051,68 USD
  
  [Orçamento da Modernização](https://calculator.aws/#/estimate?id=4905e10240762188ca7d4e71aa938b5e244010b3)

---

## Entre em Contato

Para mais informações sobre este projeto ou para solicitar suporte técnico, entre em contato com a equipe da **TI SOLUÇÕES INCRÍVEIS**:

**Informações de Contato:**
- 📧 E-mail: [ti.solucoes.incriveis@fastengineering.com](mailto:ti.solucoes.incriveis@fastengineering.com)
- 📞 Telefone: +55 (11) 1234-4321 (das 8h às 18h, horário de Brasília)
- 🌐 Site: [www.tisolucoesincriveis.com.br](https://www.tisolucoesincriveis.com.br)

**Endereço Corporativo:**  
Av. das Migrações Cloud, 1001 - Conjunto 200  

---

![ti soluçoes incriveis](imgs/tisoluçoes.png)


