# AWS SSM Deploy

목차

- [AWS SSM Deploy](#aws-ssm-deploy)
  - [실행](#실행)
  - [Inputs](#inputs)
  - [예제](#예제)

## 실행

이 액션은 AWS SSM의 `send-command` API를 호출하여 배포를 시작합니다. 배포가 완료될 때까지 대기하고, 배포가 성공적으로 완료되었는지 확인합니다. 이후, `PRUNE`이 `true`로 설정되어 있으면 이전 이미지를 정리합니다.

## Inputs

| 이름 | 설명 | 필수 | 기본값 |
| --- | --- | --- | --- |
| EC2 | EC2 인스턴스 ID | `true` | - |
| IMAGE | Docker 이미지 이름 | `true` | - |
| ECR | ECR 저장소 URL | `true` | - |
| HOST_PORT | 컨테이너를 실행할 포트 | `true` | - |
| CONTAINER_PORT | 컨테이너 포트 | `true` | - |
| WORKDIR | 배포 스크립트를 실행할 작업 디렉토리 | `false` | `/home/ec2-user/deploy` |
| SCRIPT | 실행할 배포 스크립트 | `false` | `deploy1.sh` |
| PRUNE | 이전 이미지를 정리할지 여부 | `false` | `false` |

## 예제

다음은 이 액션을 사용하는 예제입니다.

```yaml
- name: Deploy using AWS SSM
  uses: aws-actions/aws-ssm-deploy@v0.0.1
  with:
    EC2: 'i-1234567890abcdef0'
    IMAGE: 'my-docker-image'
    ECR: '123456789012.dkr.ecr.us-east-1.amazonaws.com/my-docker-image'
    HOST_PORT: '80'
    CONTAINER_PORT: '80'
    WORKDIR: '/home/ec2-user/deploy'
    SCRIPT: 'deploy1.sh'
    PRUNE: 'false'
```
