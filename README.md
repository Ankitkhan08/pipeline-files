# Jenkins CI/CD Pipeline — Java App to AWS ECS Fargate

A production-style Jenkins pipeline that takes a Java Spring Boot 
application from a GitHub commit to a live deployment on AWS ECS 
Fargate — fully automated, zero manual steps.

## Pipeline Flow
```
GitHub Push
    │
    ▼
Maven Unit Tests
    │
    ▼
Maven Build → ROOT.war artifact
    │
    ▼
Checkstyle Code Analysis
    │
    ▼
SonarQube Static Analysis
    │
    ▼
Quality Gate Check (fails build if not met)
    │
    ▼
Nexus Artifact Upload (versioned by BUILD_ID)
    │
    ▼
Docker Multi-Stage Build → ECR Image Push
    │
    ▼
ECS Fargate Deployment (force new deployment)
    │
    ▼
Slack Notification (SUCCESS / FAILURE)
```

## What this pipeline does
- Runs unit tests and fails fast if they break
- Enforces code quality via SonarQube quality gates — 
  build stops if quality standards aren't met
- Versions every artifact by Jenkins BUILD_ID and 
  stores in Nexus for traceability
- Builds a Docker image using multi-stage Dockerfile 
  and pushes to AWS ECR
- Deploys to ECS Fargate by triggering a forced 
  service update — no downtime

## Tech Stack
- **CI/CD:** Jenkins
- **Build:** Maven 3.9, JDK 17
- **Code Quality:** SonarQube, Checkstyle
- **Artifact Storage:** Nexus Repository Manager
- **Containerisation:** Docker (multi-stage builds)
- **Registry:** AWS ECR
- **Deployment:** AWS ECS Fargate
- **Notifications:** Slack

## Key Design Decisions
- Quality Gate is enforced before any artifact is published — 
  bad code never reaches Nexus or ECR
- Multi-stage Docker build keeps the final image small by 
  separating build and runtime layers
- Slack notifications on every build outcome so the team 
  always knows pipeline status without checking Jenkins

## Prerequisites
- Jenkins with plugins: Maven, SonarQube, Nexus Artifact Uploader, 
  Docker Pipeline, AWS Credentials, Slack Notification
- SonarQube server configured in Jenkins
- AWS credentials stored in Jenkins credential store
- Nexus repository set up and accessible
