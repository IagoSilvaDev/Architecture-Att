Segue abaixo o documento revisado, com a correção da imagem da VPN, mais detalhes sobre os serviços e instruções de como adicionar o diagrama:

---

# Documentação do Projeto de Migração – Fast Engineering S/A

## 1. Contexto

Somos a empresa **Fast Engineering S/A** e estamos em parceria com a **TI SOLUÇÕES INCRÍVEIS** para atender à crescente demanda do nosso eCommerce, que atualmente enfrenta limitações de escalabilidade e desempenho. Nossa infraestrutura on-premise é composta por:

- **Banco de Dados MySQL**:  
  - 01 servidor dedicado com aproximadamente 500GB de dados, 10GB de RAM e 3 Core CPU.
- **Frontend**:  
  - 01 servidor que hospeda a aplicação em **React**, com 5GB de armazenamento, 2GB de RAM e 1 Core CPU.
- **Backend**:  
  - 01 servidor executando 3 APIs, utilizando o **Nginx** como balanceador de carga e armazenando arquivos estáticos (fotos, links), com 5GB de armazenamento, 4GB de RAM e 2 Core CPU.

Diante desse cenário, a migração para a AWS é imprescindível. Inicialmente, será realizada a migração “Lift-and-Shift” (As-Is), replicando a infraestrutura atual na nuvem com o mínimo de alterações para garantir uma transição rápida e segura. Em etapa posterior, promoveremos a modernização com a adoção de Kubernetes e outras práticas avançadas.

---

## 2. Entrega do Projeto – Etapa 1: Lift-and-Shift (As-Is)

### 2.1 Escopo Detalhado

A estratégia “Lift-and-Shift” consiste em:
- Replicar a infraestrutura atual na AWS sem alterações significativas na arquitetura.
- Minimizar o tempo de inatividade durante a migração.
- Garantir a integridade, segurança e continuidade dos dados e serviços.
- Preparar o ambiente para futuras modernizações com Kubernetes.

### 2.2 Atividades Necessárias para a Migração

1. **Preparação dos Ambientes**  
   - **Configuração dos Ambientes On-Premise e AWS**:  
     - Criação de sub-redes específicas na AWS para áreas de testes e replicação.
     - Ajuste e preparação dos servidores on-premise para iniciar o processo de migração.
   - **Instalação dos Agentes de Replicação**:  
     - Instalação do AWS Replication Agent nos servidores (Banco de Dados, Frontend e Backend) para iniciar a replicação dos dados e aplicações.

2. **Configuração de Rede e Endpoints**  
   - **Comunicação Segura**:  
     - Configuração de HTTPS (porta 443) para a comunicação entre os servidores on-premise e os endpoints do AWS Application Migration Service.
     - Criação de endpoints de interface para serviços como Amazon EC2 e Amazon S3, além da configuração de um endpoint de gateway para o Amazon S3 na VPC de destino.
   - **Segmentação de Rede**:  
     - Criação de sub-redes dedicadas para replicação e testes, garantindo controle e segurança do tráfego.

3. **Replicação e Sincronização de Dados**  
   - **Replicação Contínua**:  
     - Estabelecimento de conexão contínua entre os servidores on-premise e os servidores de replicação na AWS, utilizando VPN ou AWS Direct Connect. A conexão é protegida por criptografia AES de 256 bits para garantir a transferência segura dos dados.
   - **Migração do Banco de Dados**:  
     - Uso do AWS Database Migration Service (DMS) para migrar o banco de dados MySQL para o Amazon RDS, assegurando a integridade dos dados durante o processo.
   - **Snapshots e Backup**:  
     - Execução de snapshots periódicos dos volumes do Amazon Elastic Block Store (EBS) para possibilitar a restauração dos dados em caso de falhas.

4. **Testes e Validação**  
   - **Instâncias de Teste**:  
     - Lançamento de instâncias EC2 em uma sub-rede de teste para validar a integridade dos dados e a operacionalidade das aplicações migradas.
   - **Verificação de Segurança**:  
     - Testes rigorosos das configurações de segurança, assegurando que apenas o tráfego necessário seja permitido entre os ambientes.

---

## 3. Ferramentas e Serviços Utilizados

A tabela abaixo apresenta todos os serviços contidos no diagrama de migração, com uma breve descrição e as imagens ilustrativas:

| Ícone | Serviço | Descrição |
|-------|---------|-----------|
| <img src="https://cloud-icons.onemodel.app/aws/Architecture-Service-Icons_01312023/Arch_Migration-Transfer/64/Arch_AWS-Application-Migration-Service_64@5x.png" width="50"> | **AWS Application Migration Service** | Automatiza a migração de aplicações on-premises para a AWS, simplificando o processo de replicação e minimizando o tempo de inatividade. |
| <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRULf2JOHbvkPux8pEzQrkH70TVSpfgRMzgQA&s" width="50"> | **Amazon EC2** | Proporciona instâncias de servidores virtuais para hospedar as aplicações migradas, oferecendo escalabilidade e flexibilidade operacional. |
| <img src="https://cdn.worldvectorlogo.com/logos/amazon-s3-simple-storage-service.svg" width="50"> | **Amazon S3** | Serviço de armazenamento de objetos escalável, utilizado para backups, armazenamento temporário e arquivamento de dados durante a migração. |
| <img src="https://cdn.worldvectorlogo.com/logos/amazon-elastic-block-store-1.svg" width="50"> | **Amazon Elastic Block Store (EBS)** | Armazenamento em bloco para instâncias EC2, permitindo a replicação fiel dos volumes de dados dos servidores on-premise. |
| <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQ7L7fI-Ozxh2ni9T2E7rgX_CU-VNMOpoXfwpIxYIaifUcJL_NQ0ZJi8mGHWNRdiFXmres&usqp=CAU" width="50"> | **Amazon RDS** | Banco de dados relacional gerenciado que hospedará o MySQL migrado, oferecendo alta disponibilidade e suporte Multi-AZ. |
| <img src="https://cloud-icons.onemodel.app/aws/Architecture-Service-Icons_01312023/Arch_Database/64/Arch_AWS-Database-Migration-Service_64@5x.png" width="50"> | **AWS Database Migration Service (DMS)** | Facilita a migração de bancos de dados com replicação contínua e mínima interrupção, assegurando a integridade dos dados. |
| <img src="https://github.com/user-attachments/assets/74224709-c040-46dd-858c-6307886fb90d" width="50"> | **Amazon VPC** | Permite o isolamento e segmentação da rede na AWS, possibilitando a criação de sub-redes, endpoints e o controle do tráfego entre os ambientes. |
| <img src="https://cloud-icons.onemodel.app/aws/Architecture-Service-Icons_01312023/Arch_Security-Identity-Compliance/64/Arch_AWS-Identity-and-Access-Management_64.png" width="50"> | **AWS Identity and Access Management (IAM)** | Gerencia o acesso e as permissões de usuários e serviços, garantindo que somente operações autorizadas sejam realizadas. |
| <img src="https://cloud-icons.onemodel.app/aws/Architecture-Service-Icons_01312023/Arch_Security-Identity-Compliance/64/Arch_AWS-Key-Management-Service_64.png" width="50"> | **AWS Key Management Service (KMS)** | Facilita a criação, gerenciamento e controle das chaves de criptografia, protegendo os dados em trânsito e em repouso. |
| <img src="https://icon.icepanel.io/AWS/svg/Networking-Content-Delivery/Client-VPN.svg" width="50"> | **AWS Virtual Private Network (VPN)** | Estabelece conexões seguras entre o ambiente on-premise e a AWS, garantindo a transferência segura dos dados durante a migração. |
| <img src="https://cloud-icons.onemodel.app/aws/Architecture-Service-Icons_01312023/Arch_Management-Governance/64/Arch_Amazon-CloudWatch_64.png" width="50"> | **Amazon CloudWatch** | Serviço de monitoramento e observabilidade que coleta métricas, logs e eventos, permitindo o acompanhamento da performance e integridade dos recursos migrados. |

---

## 4. Diagrama da Infraestrutura na AWS (Lift-and-Shift)

O diagrama a seguir ilustra a replicação dos componentes do ambiente atual na AWS, demonstrando a interligação entre os servidores on-premise e os serviços AWS:

- **Ambiente On-Premise**: Servidores de Banco de Dados, Frontend e Backend.
- **Conectividade Segura**: Estabelecida via VPN (ou AWS Direct Connect, conforme configuração) para a transferência segura dos dados.
- **Replicação e Sincronização**: Gerenciada pelo AWS Application Migration Service e AWS DMS.
- **Infraestrutura AWS**:  
  - **Amazon EC2**: Hospeda as aplicações migradas.  
  - **Amazon EBS**: Replica os volumes de dados dos servidores.  
  - **Amazon RDS**: Gerencia o banco de dados MySQL migrado.  
  - **Amazon S3**: Armazena backups, logs e dados temporários.  
  - **Amazon VPC, IAM, KMS e CloudWatch**: Asseguram o isolamento, controle de acesso, criptografia e monitoramento da infraestrutura.

### Como Adicionar o Diagrama

   ![Diagrama da Infraestrutura](./imgs/AWS_Migration.drawio.png)
   
## 5. Requisitos de Segurança e Processos de Backup

### 5.1 Requisitos de Segurança

- **Comunicação Segura**:  
  - Uso de HTTPS (porta 443) para comunicação entre os servidores on-premise e a AWS.  
  - Conexões seguras via VPN ou AWS Direct Connect.
- **Controle de Acesso**:  
  - Configuração de regras de segurança (grupos e Network ACLs) para permitir apenas o tráfego necessário.  
  - Gerenciamento de permissões através do AWS IAM.
- **Criptografia**:  
  - Uso do AWS KMS para a criação e controle das chaves de criptografia, protegendo os dados em trânsito e em repouso.
- **Isolamento de Rede**:  
  - Criação de sub-redes dedicadas (via Amazon VPC) para replicação e testes, garantindo segmentação e controle do tráfego.

### 5.2 Processos de Backup

- **Snapshots dos Volumes EBS**:  
  - Execução periódica de snapshots para permitir a restauração dos dados em caso de falhas.
- **Backups Automatizados do Banco de Dados**:  
  - Configuração do Amazon RDS para realizar backups automáticos do MySQL migrado.
- **Armazenamento Seguro**:  
  - Uso do Amazon S3 para arquivar logs, cópias de segurança e demais dados críticos.

---

## 6. Estimativa dos Custos da Infraestrutura

Utilizando o **AWS Calculator**, foram estimados os custos para a infraestrutura migrada, considerando:

- **Instâncias EC2**: Custos baseados na replicação dos servidores de Frontend e Backend.
- **Amazon RDS**: Custos da migração e operação do banco de dados MySQL.
- **Armazenamento**: Despesas relacionadas ao uso do Amazon S3 e dos volumes EBS.
- **Serviços de Migração**: Custos relativos ao AWS Application Migration Service e AWS DMS.
- **Custos Adicionais**: Despesas associadas ao uso do Amazon VPC, AWS IAM, AWS KMS, AWS VPN (ou Direct Connect) e monitoramento via Amazon CloudWatch.

*Observação*: Os custos são proporcionais ao tempo de utilização dos serviços e serão ajustados conforme o período de replicação (por exemplo, custo estimado para 3 dias de migração).
