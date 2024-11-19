# Proposta eCommerce

> **Região Selecionada:** South America (São Paulo) [sa-east-1]
> **Motivo:** Esta região oferece baixa latência para usuários na América do Sul, mantendo alta escalabilidade e opções de custo para suportar o crescimento contínuo do e-commerce.

## 1. Configuração da Infraestrutura de Rede

### 1.1 Criação da VPC (Virtual Private Cloud)
1. Acesse o serviço **VPC** no Console da AWS, região **sa-east-1**.
2. Clique em **Criar VPC** e configure:
   - **Nome:** VPC-eCommerce
   - **Bloco CIDR:** 10.0.0.0/16
   - **Suporte a DNS:** Ativado
3. Clique em **Criar**.

### 1.2 Configuração de Sub-redes
1. Crie sub-redes públicas e privadas para permitir alta disponibilidade:
   - **Sub-rede pública (sa-east-1a):** 10.0.1.0/24
   - **Sub-rede privada (sa-east-1a):** 10.0.2.0/24
   - **Sub-rede pública (sa-east-1b):** 10.0.3.0/24
   - **Sub-rede privada (sa-east-1b):** 10.0.4.0/24

### 1.3 Configuração da Internet Gateway e Tabela de Rotas
1. No console da **VPC**, crie um **Internet Gateway** e conecte-o à VPC.
2. Configure tabelas de rotas:
   - **Tabela de Rotas Públicas:** permite acesso externo para as sub-redes públicas.
   - **Tabela de Rotas Privadas:** usada para sub-redes privadas com acesso restrito.

---

## 2. Configuração do Banco de Dados

### 2.1 Configuração do Amazon Aurora (MySQL-compatible)
1. Acesse o serviço **Amazon RDS** no Console da AWS.
2. Clique em **Criar Banco de Dados** e selecione **Aurora (MySQL-Compatible)**.
3. Configure:
   - **Tipo de Implantação:** Multi-AZ com réplicas de leitura.
   - **Classe da Instância:** db.r5.large (ou uma instância menor como db.r5.large para otimizar custos).
   - **Nome do Banco de Dados:** ecommerceDB
   - **Credenciais:** Crie um usuário e senha para acesso ao banco.
4. Em **Configurações de Rede e Segurança**, escolha a VPC criada e selecione as sub-redes privadas.
5. Configure **Backup** e **Retenção Automática** para garantir segurança dos dados.

---

## 3. Configuração de Armazenamento e CDN

### 3.1 Armazenamento de Arquivos com Amazon S3
1. Acesse o serviço **S3** no Console da AWS.
2. Crie um novo bucket com o nome **ecommerce-static-assets**.
3. Configure permissões de leitura para conteúdo público (somente para arquivos públicos).
4. Habilite **Versionamento** para permitir recuperação de versões antigas de arquivos.

### 3.2 Configuração de Distribuição CDN com Amazon CloudFront
1. Acesse o serviço **CloudFront** e crie uma nova distribuição.
2. Configure:
   - **Origem:** bucket S3 (ecommerce-static-assets)
   - **Configurações de Cache:** Defina o TTL padrão para balancear desempenho e custo.
   - **Segurança:** Habilite HTTPS para entrega segura de conteúdo.
3. Acesse a opção de **Region Mapping** e configure para direcionar para **sa-east-1** para baixa latência local.

---

## 4. Configuração de Kubernetes com Amazon EKS

### 4.1 Criar Cluster EKS
1. Acesse o serviço **Amazon EKS** no Console da AWS na região **sa-east-1**.
2. Clique em **Criar Cluster** e configure:
   - **Nome:** ecommerce-cluster
   - **Versão do Kubernetes:** Última versão estável.
   - **VPC e Sub-redes:** Selecione a VPC e as sub-redes privadas criadas.
3. Conclua a criação do cluster e configure permissões de usuário IAM para acessar o cluster.

### 4.2 Configurar Nós de Trabalho (Worker Nodes)
1. No console do EKS, vá para a seção **Configurações** > **Nós** e crie um grupo de nós.
2. Selecione a **classe de instância EC2** (como t3.medium) para balancear custo e desempenho.
3. Ative o **Auto Scaling** para escalabilidade automática.

### 4.3 Implantação de Aplicações
1. Conecte-se ao cluster usando `kubectl`.
2. Crie arquivos de configuração YAML para cada componente:
   - **Deployment:** para a aplicação React.
   - **Service:** para expor o frontend.
   - **Ingress:** para gerenciar o tráfego HTTP/S externo.
3. Implante os contêineres com o comando `kubectl apply -f <config-file>.yaml`.

---

## 5. Configuração de Balanceamento de Carga

### 5.1 Configurar Application Load Balancer (ALB)
1. Acesse o serviço **EC2** e clique em **Load Balancers**.
2. Crie um novo **Application Load Balancer** com as seguintes configurações:
   - **Nome:** ecommerce-ALB
   - **Tipo de Rede:** Internet-facing (para acesso público)
   - **Zonas de Disponibilidade:** Selecione as sub-redes públicas.
3. Adicione **Listeners**:
   - Porta **80** e **443** (HTTPS).
4. Configure verificações de integridade para monitorar o status dos nós.

---

## 6. Configuração de Segurança e Monitoramento

### 6.1 Configurar Segurança com AWS WAF e Shield
1. No console do **AWS WAF**, crie um **Web ACL** para a aplicação.
2. Adicione regras de firewall para bloquear ataques comuns (como SQL Injection).
3. Ative o **AWS Shield** para proteção básica contra DDoS.

### 6.2 Monitoramento com Amazon CloudWatch
1. Configure **CloudWatch Logs** e **Métricas** para monitorar o desempenho.
2. Habilite alarmes para monitorar uso de CPU, tráfego de rede e status do banco de dados.

### 6.3 Configuração de Backups Automatizados
1. No console do **AWS Backup**, crie uma **Política de Backup** para o banco de dados Aurora e volumes de dados importantes.
2. Configure a retenção de backups e crie um cronograma de backup diário.

---

## Conclusão

Esta configuração detalhada usa a região South America (São Paulo) para otimizar custos e manter baixa latência. Os serviços AWS, como Amazon EKS, Aurora, S3, CloudFront e ALB, proporcionam uma infraestrutura flexível e segura para suportar o aumento de tráfego mensal de 20% esperado para o e-commerce.
