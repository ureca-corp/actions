# AWS SSM Deployment Action Guide

## What's This All About?
This GitHub Action automates deployments to EC2 instances using AWS Systems Manager (SSM).<br/>
Instead of dealing with SSH connections, it provides a secure and efficient way to execute deployment scripts on your EC2 instances.<br/>
It's designed to make your deployment process both secure and straightforward! 🚀

## Main Features
The deployment action handles these key tasks:
- Executes scripts securely on EC2 instances without SSH
- Provides real-time deployment status updates
- Performs thorough validation of execution results

## Input Parameters
Here's what you'll need to get started:

| Parameter         | Required | Description                              | Example             |
| ----------------- | -------- | ---------------------------------------- | ------------------- |
| instance-id       | Yes      | Your EC2 instance's unique identifier    | i-1234567890abcdef0 |
| working-directory | Yes      | Target directory path on EC2 instance    | /home/ec2-user/app  |
| script-name       | Yes      | Name of the deployment script to execute | deploy.sh           |

## How It Works
Let's walk through the process:

1. **Command Initiation**
  - Establishes connection through AWS SSM
  - Transmits your script with specified parameters
  - Generates a tracking ID for deployment monitoring

2. **Execution Monitoring**
  - Tracks deployment progress in real-time
  - Maintains active status monitoring
  - Provides progress updates throughout the process

3. **Validation & Completion**
  - Performs comprehensive execution validation
  - Immediately notifies of any issues
  - Confirms successful deployment completion

---

# AWS SSM 배포 액션 가이드

## 소개
이 GitHub Action은 AWS Systems Manager(SSM)를 활용하여 EC2 인스턴스 배포를 자동화합니다.<br/>
SSH 연결 없이도 EC2 인스턴스에서 배포 스크립트를 안전하고 효율적으로 실행할 수 있습니다.<br/>
배포 프로세스를 안전하고 간단하게 만들어드립니다! 🚀

## 주요 기능
배포 액션은 다음과 같은 핵심 작업을 수행합니다:
- SSH 없이 EC2 인스턴스에서 스크립트를 안전하게 실행
- 실시간 배포 상태 업데이트 제공
- 실행 결과에 대한 철저한 검증 수행

## 입력 파라미터
시작하기 위해 필요한 정보입니다:

| 파라미터          | 필수 여부 | 설명                              | 예시                |
| ----------------- | --------- | --------------------------------- | ------------------- |
| instance-id       | 예        | EC2 인스턴스의 고유 식별자        | i-1234567890abcdef0 |
| working-directory | 예        | EC2 인스턴스의 대상 디렉토리 경로 | /home/ec2-user/app  |
| script-name       | 예        | 실행할 배포 스크립트 이름         | deploy.sh           |

## 동작 방식
프로세스를 단계별로 살펴보겠습니다:

1. **명령 초기화**
  - AWS SSM을 통한 연결 수립
  - 지정된 파라미터와 함께 스크립트 전송
  - 배포 모니터링을 위한 추적 ID 생성

2. **실행 모니터링**
  - 실시간 배포 진행 상황 추적
  - 지속적인 상태 모니터링 유지
  - 전체 프로세스 진행 상황 제공

3. **검증 및 완료**
  - 종합적인 실행 검증 수행
  - 문제 발생 시 즉각적인 알림
  - 성공적인 배포 완료 확인

## 더 자세한 정보
AWS SSM CLI에 대한 자세한 내용은 다음 공식 문서를 참조하실 수 있습니다:  
📚 https://docs.aws.amazon.com/cli/latest/reference/ssm/index.html