---
layout: post
title: Criando e Implantando um Cluster AWS EKS com Terraform
date: 2025-01-30 10:30 -0300
categories: [Terraform]
tags: [AWS EKS, Terraform, Kubernetes]
slug: 'AWS-EKS-Cluster-Com-Terraform'
---

![alt text](/assets/img/01/terraform-aws-eks-cluster.png)
_Terraform-AWS-EKS-Cluster_

Neste artigo, vamos explorar como criar e implantar um cluster **Amazon EKS** com **Terraform**, oferecendo uma solução automatizada e escalável para gerenciar sua infraestrutura Kubernetes na AWS.

O **Amazon EKS** (Elastic Kubernetes Service) é o serviço gerenciado da AWS que simplifica a execução e o dimensionamento de aplicativos Kubernetes, tanto na nuvem quanto em ambientes on-premises, tornando a gestão de containers mais ágil e eficiente.

Por outro lado, o **Terraform**, da HashiCorp, é a ferramenta que vai transformar sua maneira de trabalhar com infraestrutura. Com ela, você define e provisiona recursos na nuvem ou on-premises através de arquivos de configuração declarativos, garantindo versionamento, reutilização e facilidade de compartilhamento da infraestrutura.


## 1.**Pré-requisitos**

### Conta AWS 
Se você ainda não tem uma conta **AWS**, crie uma acessando o site oficial da AWS [aqui](https://aws.amazon.com/) e siga as instruções para configurar sua conta. Lembre-se de fornecer informações de cobrança precisas, mesmo que estejamos utilizando o **Free Tier**.

> *Nota: É crucial escolher a região onde você implantará os recursos. Por exemplo, vou provisionar recursos para este projeto na região **us-east-1**. Certifique-se de verificar a região, pois se você esquecer de encerrar os recursos, poderá enfrentar custos indesejados. Recursos não utilizados em regiões não verificadas continuarão gerando cobranças 💰. Verificar novamente a região pode ajudar a evitar custos desnecessários e garantir um gerenciamento eficiente dos recursos.*
{:.prompt-info}

### Instale o Terraform
Se você ainda não tem o **Terraform** instalado na sua máquina, faça o download da versão mais recente [aqui](https://developer.hashicorp.com/terraform/downloads).

Após a instalação, certifique-se de configurar as variáveis de ambiente para que o Terraform possa ser utilizado de qualquer diretório. Para verificar se o Terraform foi instalado corretamente, abra um terminal ou prompt de comando e digite o seguinte comando:

```bash
terraform version
```
### Configurar Credenciais da AWS

> *Nota: Essa etapa envolve a configuração de credenciais da AWS, o que é essencial para o Terraform interagir com nossa conta da AWS. O Terraform usa essas credenciais para se autenticar e fazer chamadas de API para criar e gerenciar recursos em nosso nome.*
{:.prompt-info}

Para aumentar a segurança e evitar o uso da conta de administrador para tarefas regulares, siga os passos abaixo para criar um novo usuário IAM.

- Faça login no [AWS Management Console](https://aws.amazon.com/console/).
- Pesquise por “IAM” na barra de pesquisa.
- No painel do IAM, clique em **"Usuários"** na barra lateral.
- Clique no botão **“Criar usuário”**.
- Escolha um nome para o novo usuário e clique em **Next**.
- Selecione a opção **Adicionar usuário ao grupo**.
- Clique em **Criar grupo**.
- Digite o nome do grupo e, em **Permissões**, selecione **AdministratorAccess**.
- Clique em **Criar grupo de usuário**.
- Marque a caixa do grupo criado e clique em **Next** e depois em **Criar usuário**.
- Será exibida uma faixa verde informando que o usuário foi criado. Clique em **Ver**.
- Clique em **Criar chave de acesso**.
- Selecione o tipo de chave **Interface de linha de comando (CLI)** e clique em **Next**.
- Insira uma tag para a chave (opcional) e clique em **Criar chave de acesso**.
- As credenciais **access_key** e **secret_key** serão exibidas na tela.


> *Nota: **Importante**: certifique-se de salvar a chave de acesso secreta em um local seguro. Ela só é mostrada uma vez. Se você a perder, precisará gerar um novo par de chaves.*
{:.prompt-info}

## 2. Criando Recursos no Terraform

Para começar, crie um diretório específico para o seu projeto, onde você armazenará todos os arquivos de configuração do Terraform. Esse diretório servirá como a base do seu ambiente de infraestrutura.

### Providers.tf

Vamos criar um arquivo de **providers.tf** no Terraform. Esse arquivo é essencial, pois nos permite interagir com os recursos da AWS.

```hcl
# Definindo o providers
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

# Configuração do providers AWS
provider "aws" {
  region = var.region

  access_key = var.aws_access_key
  secret_key = var.aws_secret_key
}
```
### Backend.tf

Configurar o estado remoto no Terraform significa armazenar o arquivo de estado em um local central, como o S3, e usar o DynamoDB para garantir que ninguém altere o estado ao mesmo tempo. Isso permite que todos os membros da equipe acessem e trabalhem com a versão mais atual do estado da infraestrutura.

Essa configuração é importante para evitar conflitos, melhorar a segurança e garantir que a infraestrutura seja gerida de forma eficiente em projetos colaborativos. Com o estado remoto, a equipe pode garantir que as mudanças sejam feitas de forma controlada e sem erros.