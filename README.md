# Total Absence Management Portal

> A cloud-native web portal for employee absence tracking, deployed to Azure App Services via a 3-stage CI/CD pipeline.

## Problem Statement

Most teams manage absences through spreadsheets or expensive SaaS tools. This portal provides a lightweight, self-hosted alternative built entirely on Azure with automated deployments, event-driven notifications, and role-based access control.

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

## Tech Stack

| Layer | Technology |
|---|---|
| Hosting | Azure App Service |
| Database | Azure SQL |
| Event Processing | Azure Function Apps |
| Identity & Access | Azure Active Directory (RBAC) |
| Network Security | Azure Virtual Network, NSGs |
| CI/CD | GitHub Actions (3-stage pipeline) |
| Language | Python (Flask) |
| IaC | ARM Templates |

## Features

- Submit absence requests with date range, type, and reason
- Manager approval and rejection workflow
- Email notifications via Azure Function App on status change
- Role-based access: Employee view vs Manager view via Azure AD
- Full absence history stored in Azure SQL

## CI/CD Pipeline

- Dev stage: Triggered on every push to develop branch
- Staging stage: Deploys on push to staging branch
- Production stage: Manual approval gate before deployment

## Results

- 3-stage CI/CD pipeline — Dev → Staging → Production
- Zero manual deployment effort after pipeline setup
- Role-based access enforced via Azure AD
- Network segmentation across all application tiers
- Event-driven email notifications via Function App

## Author

**Ajit Karthikeya Balla**
📧 ajitkarthikeyaballa@gmail.com
💼 [linkedin.com/in/ajitkarthikeyaballa](https://linkedin.com/in/ajitkarthikeyaballa)
