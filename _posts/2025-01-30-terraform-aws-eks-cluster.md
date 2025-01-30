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


## **Pré-requisitos**

### 1. Conta AWS 🤖
Se você ainda não tem uma conta **AWS**, crie uma acessando o site oficial da AWS [aqui](https://aws.amazon.com/) e siga as instruções para configurar sua conta. Lembre-se de fornecer informações de cobrança precisas, mesmo que estejamos utilizando o **Free Tier**.

> *Nota: É crucial escolher a região onde você implantará os recursos. Por exemplo, vou provisionar recursos para este projeto na região **us-east-1**. Certifique-se de verificar a região, pois se você esquecer de encerrar os recursos, poderá enfrentar custos indesejados. Recursos não utilizados em regiões não verificadas continuarão gerando cobranças 💰. Verificar novamente a região pode ajudar a evitar custos desnecessários e garantir um gerenciamento eficiente dos recursos 🙌.*
{:.prompt-info}

### 2. Instale o Terraform
Se você ainda não tem o **Terraform** instalado na sua máquina, faça o download da versão mais recente [aqui](https://developer.hashicorp.com/terraform/downloads).

Após a instalação, certifique-se de configurar as variáveis de ambiente para que o Terraform possa ser utilizado de qualquer diretório. Para verificar se o Terraform foi instalado corretamente, abra um terminal ou prompt de comando e digite o seguinte comando:

```bash
terraform version
```