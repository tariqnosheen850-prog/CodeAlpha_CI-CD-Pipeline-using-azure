# CodeAlpha DevOps Task 1: Azure CI/CD Pipeline

An automated CI/CD pipeline built with **Azure Pipelines**, using **Azure Container Registry (ACR)** for container image storage and **Azure App Service** for automatic web app deployment.

## Project Overview

This project demonstrates a complete DevOps workflow on Azure:

```
Code Push (GitHub) 
      -> Azure Pipeline Trigger
      -> Build Docker Image
      -> Push Image to Azure Container Registry (ACR)
      -> Deploy to Azure App Service
      -> Monitor via Azure Monitor / Pipeline Dashboard
```

## Tech Stack

- **App:** Node.js + Express (simple sample web app)
- **Containerization:** Docker
- **CI/CD:** Azure Pipelines (YAML pipeline)
- **Image Registry:** Azure Container Registry (ACR)
- **Hosting:** Azure App Service (Linux, container-based)
- **Monitoring:** Azure Monitor + Pipeline run history

## Files in This Repository

| File | Purpose |
|---|---|
| `app.js` | Sample Express web app |
| `package.json` | Node.js dependencies |
| `Dockerfile` | Instructions to containerize the app |
| `azure-pipelines.yml` | Full CI/CD pipeline definition (Build + Deploy stages) |

## Azure Resources Setup

Create the required resources using Azure CLI:

```bash
# Resource group
az group create --name CodeAlpha-RG --location eastus

# Azure Container Registry
az acr create --resource-group CodeAlpha-RG --name codealphaacr --sku Basic

# App Service Plan (Linux, container-supported)
az appservice plan create --name CodeAlpha-Plan --resource-group CodeAlpha-RG --is-linux --sku B1

# Web App
az webapp create --resource-group CodeAlpha-RG --plan CodeAlpha-Plan \
  --name codealpha-webapp \
  --deployment-container-image-name codealphaacr.azurecr.io/codealpha-app:latest
```

> Note: `codealphaacr` and `codealpha-webapp` must be globally unique names. Add a unique suffix if they are already taken.

## Azure DevOps Pipeline Setup

1. Create a project at [dev.azure.com](https://dev.azure.com) (or use an existing one).
2. Connect this GitHub repository as the pipeline source.
3. Create two **Service Connections** (Project Settings -> Service connections):
   - **Docker Registry connection** named `acr-service-connection`, linked to your ACR
   - **Azure Resource Manager connection** named `azure-arm-service-connection`, authorized against your Azure subscription
4. Create a new pipeline pointing to `azure-pipelines.yml` in this repo.
5. Push to the `main` branch to trigger the pipeline automatically.

## Pipeline Stages

**Build stage**
- Builds the Docker image from the `Dockerfile`
- Pushes the image to Azure Container Registry, tagged with the build ID and `latest`

**Deploy stage**
- Deploys the newly pushed container image to Azure App Service
- Runs automatically after a successful build

## Monitoring

- **Azure DevOps -> Pipelines**: view build/deploy status, logs, and stage duration for every run
- **Azure Monitor + Application Insights**: linked to App Service to track runtime errors, response time, and availability
- **Notifications**: configured in Azure DevOps (Project Settings -> Notifications) to alert on pipeline failure
- **Alerts**: set up in Azure Monitor for deployment failures or app downtime

## Key DevOps Concepts Demonstrated

- **Continuous Integration (CI):** code changes are automatically built on every commit to `main`
- **Continuous Deployment (CD):** successful builds are automatically deployed to production
- **Containerization:** the app is packaged with Docker for a consistent runtime environment
- **Infrastructure as Code:** the pipeline itself is defined as version-controlled YAML
- **Artifact Registry:** ACR provides traceable, versioned container images for each release

## Author

Built as part of the **CodeAlpha DevOps Internship** - Task 1.
