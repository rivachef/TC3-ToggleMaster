# ToggleMaster - Tech Challenge Fase 3

Plataforma de Feature Flags com infraestrutura automatizada, CI/CD com DevSecOps e GitOps.

## Estrutura do Projeto

```
TC3-ToggleMaster/
├── terraform/           # IaC - Infraestrutura AWS (VPC, EKS, RDS, Redis, SQS, DynamoDB, ECR)
├── microservices/       # Codigo fonte dos 5 microsservicos
├── gitops/              # Manifestos K8s monitorados pelo ArgoCD
├── argocd/              # Configuracao do ArgoCD (applications + install)
└── .github/workflows/   # Pipelines CI/CD com DevSecOps
```

## Microsservicos

| Servico | Linguagem | Porta | Funcao |
|---------|-----------|-------|--------|
| auth-service | Go 1.21 | 8001 | Gerenciamento de API keys |
| flag-service | Python 3.12 | 8002 | CRUD de feature flags |
| targeting-service | Python 3.12 | 8003 | Regras de segmentacao |
| evaluation-service | Go 1.21 | 8004 | Avaliacao de flags em tempo real |
| analytics-service | Python 3.12 | 8005 | Analytics via SQS/DynamoDB |

## Setup

### 1. Terraform (Infraestrutura)

```bash
cd terraform
cp terraform.tfvars.example terraform.tfvars
# Edite terraform.tfvars com seus valores

terraform init
terraform plan
terraform apply
```

### 2. ArgoCD

```bash
chmod +x argocd/install.sh
./argocd/install.sh

# Aplicar as applications
kubectl apply -f argocd/applications.yaml
```

### 3. GitHub Actions

Configure os seguintes secrets no repositorio GitHub:
- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`
- `AWS_SESSION_TOKEN`
- `ECR_REGISTRY` (ex: 985346208543.dkr.ecr.us-east-1.amazonaws.com)

## Pipeline CI/CD

```
Build & Test → Lint → Security Scan (SAST+SCA) → Docker Build + Trivy → Push ECR → Update GitOps
```

O ArgoCD detecta automaticamente mudancas no `gitops/` e sincroniza com o cluster EKS.
