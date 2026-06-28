# terraform-aws-ecs-asg

> Terraform으로 구성하는 **AWS ECS(EC2 Launch Type) + Auto Scaling Group** 인프라 자동화 프로젝트  
> EC2 기반 ECS 클러스터에 ASG Capacity Provider를 연동하여 자동 스케일링을 구현합니다.

## Architecture

<img width="874" alt="Architecture" src="https://user-images.githubusercontent.com/77256060/166134806-86db86d5-b9f9-438e-8ee3-cbfd94dfb866.png">

---

## Tech Stack

| Category | Tool |
|---|---|
| IaC | Terraform |
| Container Orchestration | AWS ECS (EC2 Launch Type) |
| Auto Scaling | AWS Auto Scaling Group + ECS Capacity Provider |
| Storage | AWS EFS |
| Networking | AWS VPC, ALB |

---

## 디렉토리 구조

```
.
├── vpc/    # VPC, Subnet (public / private / private-db × 2 AZ)
└── Infra/  # ECS Cluster, Task Definition, ECS Service, ALB, ASG, EFS
```

---

## Terraform Workspace 구성

> VPC → Infra 순서로 적용합니다.

| # | Workspace | 생성 리소스 |
|---|---|---|
| 1 | **VPC** | VPC, public/private/private-db Subnet × 2 AZ, IGW, NAT GW |
| 2 | **Infra** | ECS Cluster, Task Definition, ECS Service, ALB, ASG, Launch Template, EFS |

---

## 인프라 구성 흐름

1. **VPC 생성** — public, private, private-db 서브넷 각각 2개 AZ에 생성
2. **EFS 생성** — 컨테이너 간 공유 파일시스템
3. **ASG + Launch Template 생성** — Userdata: EFS 마운트 + ECS Agent 설치
4. **ALB 생성** — ECS 서비스 앞단 로드밸런서
5. **ECS Cluster 생성** — `capacity_providers`에 ASG 연동 (Auto Scaling 기반 컨테이너 스케일링)
6. **ECS Task Definition + ECS Service 배포**

---

## 사용 방법

```bash
# 1. 레포지토리 클론
git clone https://github.com/DACHANCHOI/terraform-aws-ecs-asg.git
cd terraform-aws-ecs-asg

# 2. 환경변수 설정
# Infra/terraform.auto.tfvars에서 aws_account_id 수정

# 3. Terraform Backend 수정
# vpc/_backend.tf, Infra/_backend.tf 각각 수정

# 4. 순서대로 프로비저닝
cd vpc && terraform init && terraform apply
cd ../Infra && terraform init && terraform apply
```
