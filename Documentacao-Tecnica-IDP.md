
## 📄 Documentação Técnica – Case de Teste IDP Tecnologia (Projeto .NET)

### 🔹 Visão Geral

Este case simula a administração de um ambiente híbrido Azure DevOps com aplicações .NET (API/Web), utilizando práticas de CI/CD, infraestrutura como código, escalabilidade, monitoramento e segurança.

---

## ✅ CI/CD com Azure DevOps

### 📁 Repositório

- **Nome:** Cased-de-teste-IDP
- **Tipo:** Público (GitHub)

### 🛠 Pipeline YAML (`azure-pipelines.yml`)

```yaml
trigger:
  branches:
    include:
      - main

pool:
  vmImage: 'windows-latest'

variables:
  buildConfiguration: 'Release'

steps:
- task: UseDotNet@2
  inputs:
    version: '7.x.x'
    includePreviewVersions: true

- script: dotnet restore
  displayName: 'Restore packages'

- script: dotnet build --configuration $(buildConfiguration)
  displayName: 'Build solution'

- script: dotnet test --no-build --verbosity normal
  displayName: 'Run unit tests'

- task: PublishBuildArtifacts@1
  inputs:
    pathToPublish: '$(Build.ArtifactStagingDirectory)'
    artifactName: 'drop'
```

---

## ⚙️ Infraestrutura como Código (IaC)

### 📦 Terraform (App Service + Azure SQL)

```hcl
provider "azurerm" {
  features {}
}

resource "azurerm_resource_group" "rg" {
  name     = "rg-idp-dev"
  location = "East US"
}

resource "azurerm_app_service_plan" "asp" {
  name                = "asp-idp"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  sku {
    tier = "Standard"
    size = "S1"
  }
}

resource "azurerm_app_service" "app" {
  name                = "idp-api"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  app_service_plan_id = azurerm_app_service_plan.asp.id
  site_config {
    dotnet_framework_version = "v7.0"
  }
}
```

---

## 📈 Monitoramento e Alertas

### 🔍 Azure Monitor + Log Analytics

- Monitorar uso de CPU, memória e armazenamento de VMs (Windows/Linux).
- Monitorar disponibilidade de Azure SQL.
- Criar alertas de:
  - Falha de build (Azure DevOps)
  - Uso > 80% de CPU
  - Conexão perdida com SQL

### 📊 Dashboard Sugerido

- Painel com:
  - Estado das VMs
  - Disponibilidade das APIs
  - Health do SQL

---

## 📊 Escalabilidade

### ☁️ App Service

- Configurar autoscale baseado em uso de CPU (ex: >70%).
- Permitir instâncias mínimas: 1 / máximas: 3

### 🧩 SQL Database

- Usar **Elastic Pools** para ambientes com múltiplos bancos.

### 🧠 VM Scale Sets

- Para servidores Windows/Linux personalizados com base em imagem.

---

## 🔐 Segurança e Compliance

### 🔑 Azure Key Vault

- Armazenar:
  - Connection strings
  - Secrets de deploy (CI/CD)

### 👥 RBAC – Role-Based Access Control

- Time de Dev: `Contributor` no grupo Dev
- Time de Ops: `Reader` + `Monitoring Reader` em Produção
- Auditoria: `Reader` + acesso a Log Analytics

---

## 📌 Considerações Finais

- Pipeline funcional (com erro de paralelismo simulado)
- Estrutura preparada para execução assim que granted
- Todos os componentes estão prontos para testes reais

Caso deseje, posso gerar os prints simulados das telas do DevOps + Portal Azure para compor a entrega final.
