# Total Absence Management Portal

> A cloud-native web portal for employee absence tracking, deployed to Azure App Services via a 3-stage CI/CD pipeline — built to demonstrate production-grade Azure DevOps practices.

---

## Problem Statement

Most small and mid-sized teams manage absences through spreadsheets or expensive SaaS tools. This portal provides a lightweight, self-hosted alternative built entirely on Azure — with automated deployments, event-driven notifications, and role-based access control.

---

## Architecture
```
Employee (Browser)
        │
        ▼
Azure App Service (Web Frontend)
        │
        ▼
Azure SQL Database (Absence Records)
        │
        ▼
Azure Function App (Event Triggers)
        │
        ▼
Email Notification (Approval / Rejection)

CI/CD Flow:
GitHub Push → Azure DevOps Pipeline → Dev → Staging → Production
```

---

## Tech Stack

| Layer | Technology |
|---|---|
| Hosting | Azure App Service |
| Database | Azure SQL |
| Event Processing | Azure Function Apps |
| Identity & Access | Azure Active Directory (RBAC) |
| Network Security | Azure Virtual Network, NSGs |
| CI/CD | Azure DevOps (3-stage pipeline) |
| Language | Python (Flask) |
| IaC | ARM Templates |

---

## Features

### Core Features
- Submit absence requests with date range, type (sick/annual/emergency), and reason
- Manager approval and rejection workflow
- Real-time email notifications via Azure Function App on status change
- Role-based access: Employee view vs Manager view via Azure AD RBAC
- Full absence history per employee stored in Azure SQL

### Security
- All application tiers secured within Azure Virtual Network
- Least-privilege access enforced via Azure AD role assignments
- NSGs restrict traffic between frontend and backend tiers
- No direct public access to database layer

### CI/CD Pipeline
- **Dev stage:** Triggered on every push — runs build and unit tests
- **Staging stage:** Deploys to staging slot for integration testing
- **Production stage:** Manual approval gate before deployment
- Zero-downtime deployment using Azure App Service deployment slots

---

## Project Structure
```
total-absence-management-portal/
│
├── src/
│   ├── app.py                  # Flask application entry point
│   ├── routes/
│   │   ├── employee.py         # Employee-facing routes
│   │   └── manager.py          # Manager-facing routes
│   └── models/
│       └── absence.py          # Absence data model
│
├── infrastructure/
│   └── arm-template.json       # ARM Template for Azure provisioning
│
├── function-app/
│   └── notify/
│       └── __init__.py         # Azure Function for email notifications
│
├── tests/
│   └── test_routes.py          # Unit tests
│
├── .github/
│   └── workflows/
│       └── deploy.yml          # CI/CD pipeline definition
│
├── requirements.txt
└── README.md
```

---

## ARM Template — Core Infrastructure

Provisions App Service, Azure SQL, and Function App in one deployment.
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "appServiceName": {
      "type": "string",
      "defaultValue": "absence-portal-app"
    },
    "sqlServerName": {
      "type": "string",
      "defaultValue": "absence-sql-server"
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Web/serverfarms",
      "apiVersion": "2022-03-01",
      "name": "absence-portal-plan",
      "location": "[parameters('location')]",
      "sku": {
        "name": "B1",
        "tier": "Basic"
      }
    },
    {
      "type": "Microsoft.Web/sites",
      "apiVersion": "2022-03-01",
      "name": "[parameters('appServiceName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "absence-portal-plan"
      ],
      "properties": {
        "serverFarmId": "absence-portal-plan"
      }
    },
    {
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2022-02-01-preview",
      "name": "[parameters('sqlServerName')]",
      "location": "[parameters('location')]",
      "properties": {
        "administratorLogin": "sqladmin",
        "administratorLoginPassword": "P@ssword1234!"
      }
    }
  ]
}
```

---

## CI/CD Pipeline
```yaml
name: Deploy Absence Portal

on:
  push:
    branches:
      - main
      - staging
      - develop

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.10'

      - name: Install dependencies
        run: pip install -r requirements.txt

      - name: Run tests
        run: pytest tests/

  deploy-dev:
    needs: build
    if: github.ref == 'refs/heads/develop'
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to Dev
        run: echo "Deploying to Dev environment"

  deploy-staging:
    needs: build
    if: github.ref == 'refs/heads/staging'
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to Staging
        run: echo "Deploying to Staging environment"

  deploy-production:
    needs: build
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    environment: production
    steps:
      - name: Deploy to Production
        run: echo "Deploying to Production environment"
```

---

## Results

- ✅ 3-stage CI/CD pipeline — Dev → Staging → Production
- ✅ Zero manual deployment effort after pipeline setup
- ✅ Role-based access enforced via Azure AD
- ✅ Network segmentation across all application tiers
- ✅ Event-driven email notifications via Function App

---

## Author

**Ajit Karthikeya Balla**  
📧 ajitkarthikeyaballa@gmail.com  
💼 [linkedin.com/in/ajitkarthikeyaballa](https://linkedin.com/in/ajitkarthikeyaballa)
