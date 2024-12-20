# ECR Docker 배포 스크립트 가이드

# 목차

1. [개요](#개요)

2. [주요 기능](#주요-기능)

3. [기본 사용법](#기본-사용법)
  - [필수 파라미터만 사용](#1-필수-파라미터만-사용)
  - [모든 파라미터 사용](#2-모든-파라미터-사용)

4. [매개변수 설명](#매개변수-설명)
  - [필수 매개변수](#필수-매개변수)
  - [선택 매개변수](#선택-매개변수)

5. [동작 프로세스](#동작-프로세스)
  - [초기화](#1-초기화)
  - [ECR 인증](#2-ecr-인증)
  - [컨테이너 배포](#3-컨테이너-배포)
  - [설정](#4-설정)

6. [실제 서비스별 예시](#실제-서비스별-예시)
  - [Node.js 애플리케이션](#1-nodejs-애플리케이션)
  - [Spring Boot 애플리케이션](#2-spring-boot-애플리케이션)
  - [Nginx 웹 서버](#3-nginx-웹-서버)

7. [문제 해결 가이드](#문제-해결-가이드)
  - [인증 실패 문제](#1-인증-실패-문제)
    - [AWS 자격 증명 오류](#11-aws-자격-증명-오류)
    - [ECR 저장소 권한 문제](#12-ecr-저장소-권한-문제)

8. [참고 문서](#참고-문서)

## 개요
이 스크립트는 Amazon ECR(Elastic Container Registry)에서 Docker 컨테이너를 배포하는 프로세스를 자동화합니다.<br/>
ECR 인증, 컨테이너 관리, 포트 매핑을 포함한 설정 가능한 매개변수를 통한 배포를 처리합니다. 🐳

## 주요 기능
- ECR 인증 및 이미지 가져오기
- 자동 컨테이너 정리 및 재배포
- 컨테이너 포트 매핑
- 컨테이너 설정 커스터마이징
- 오류 처리 및 상태 보고

## 기본 사용법

### 1. 필수 파라미터만 사용
```bash
./deploy.sh \
   --image-name my-application \
   --ecr-url my-app.ecr.ap-northeast-2.amazonaws.com \
   --port 3000
```

### 2. 모든 파라미터 사용
```bash
./deploy.sh \
    --image-name my-application \
    --ecr-url my-app.ecr.ap-northeast-2.amazonaws.com \
    --port 3000 \
    --aws-region ap-northeast-2 \
    --image-tag v1.0.0 \
    --container-name my-custom-container
```

## 매개변수 설명

### 필수 매개변수

| 매개변수   | 설명                 | 예시                                    |
| ---------- | -------------------- | --------------------------------------- |
| image-name | Docker 이미지 이름   | my-application                          |
| ecr-url    | ECR 레지스트리 URL   | my-app.ecr.ap-northeast-2.amazonaws.com |
| port       | 노출할 컨테이너 포트 | 3000                                    |

### 선택 매개변수

| 매개변수       | 설명               | 기본값             |
| -------------- | ------------------ | ------------------ |
| aws-region     | AWS 리전           | ap-northeast-2 (서울)     |
| image-tag      | Docker 이미지 태그 | latest             |
| container-name | 컨테이너 이름      | 이미지 이름과 동일 |

## 동작 프로세스

### 1. 초기화
- 필수 매개변수 검증 (image-name, ecr-url, port)
- 선택적 매개변수 기본값 설정
- 컨테이너 이름 미지정 시 이미지 이름으로 설정

### 2. ECR 인증
- AWS 자격 증명을 사용하여 Amazon ECR 로그인
- 지정된 ECR 저장소 접근 권한 확인
- 인증 오류 발생 시 처리

### 3. 컨테이너 배포
스크립트는 다음 단계를 수행합니다:

**이미지 관리**
- ECR에서 지정된 Docker 이미지 가져오기
- 다운로드 후 이미지 무결성 검증

**컨테이너 수명주기**
- 기존 컨테이너 확인
- 기존 컨테이너 안전 중지 및 제거
- 지정된 설정으로 새 컨테이너 배포

### 4. 설정
컨테이너는 다음과 같이 구성됩니다:
- 사용자 지정 컨테이너 이름
- 자동 재시작 정책
- 호스트와 컨테이너 간 포트 매핑
- 필수 Docker 런타임 설정

## 실제 서비스별 예시
### 1. Node.js 애플리케이션
```bash
./deploy.sh \
    --image-name node-app \
    --ecr-url 123456789012.dkr.ecr.ap-northeast-2.amazonaws.com \
    --port 3000 \
    --image-tag latest
```

### 2. Spring Boot 애플리케이션
```bash
./deploy.sh \
    --image-name spring-app \
    --ecr-url 123456789012.dkr.ecr.ap-northeast-2.amazonaws.com \
    --port 8080 \
    --image-tag latest
```

### 3. Nginx 웹 서버
```bash
./deploy.sh \
    --image-name nginx-web \
    --ecr-url 123456789012.dkr.ecr.ap-northeast-2.amazonaws.com \
    --port 80 \
    --container-name web-server
```

## 문제 해결 가이드

### 1. 인증 실패 문제

#### 1.1 AWS 자격 증명 오류
**증상**
- `An error occurred (UnauthorizedOperation)` 메시지 발생
- `Unable to locate credentials` 오류
- ECR 로그인 시도 시 인증 실패

**해결 방법**
```bash
# AWS 자격 증명 확인
aws configure list

# AWS 자격 증명 재설정
aws configure

# ECR 로그인 테스트
aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${ECR_URL}
```

#### 1.2 ECR 저장소 권한 문제
**증상**
- `Repository policy does not allow` 오류
- `AccessDeniedException` 발생

**해결 방법**
1. IAM 사용자 정책 확인
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ecr:GetAuthorizationToken",
                "ecr:BatchCheckLayerAvailability",
                "ecr:GetDownloadUrlForLayer",
                "ecr:BatchGetImage"
            ],
            "Resource": "*"
        }
    ]
}
```

2. ECR 저장소 정책 확인 및 수정
```bash
aws ecr get-repository-policy --repository-name ${IMAGE_NAME}
```

## 참고 문서

### AWS 공식 문서
- [Amazon ECR 사용 설명서](https://docs.aws.amazon.com/ko_kr/AmazonECR/latest/userguide/what-is-ecr.html)
- [AWS CLI 설정 가이드](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html)
- [AWS IAM 정책 설정](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html)
- [ECR 리포지토리 정책 설정](https://docs.aws.amazon.com/AmazonECR/latest/userguide/repository-policies.html)

### Docker 공식 문서
- [Docker Run 레퍼런스](https://docs.docker.com/engine/reference/run/)
- [Docker 네트워크 설정 가이드](https://docs.docker.com/config/containers/container-networking/)
- [Docker 컨테이너 로깅](https://docs.docker.com/config/containers/logging/)
- [Docker 볼륨 관리](https://docs.docker.com/storage/volumes/)
- [Docker 보안 설정](https://docs.docker.com/engine/security/)

### 모니터링 및 트러블슈팅
- [Docker 디버깅 가이드](https://docs.docker.com/engine/reference/commandline/logs/)
- [컨테이너 모니터링 도구](https://docs.docker.com/config/containers/resource_constraints/)
- [Docker 성능 최적화](https://docs.docker.com/config/containers/resource_constraints/)

### AWS 보안 관련
- [ECR 보안 모범 사례](https://docs.aws.amazon.com/AmazonECR/latest/userguide/security-best-practices.html)
- [AWS 자격 증명 관리](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html)
- [VPC 엔드포인트 설정](https://docs.aws.amazon.com/vpc/latest/privatelink/vpc-endpoints.html)

### 추가 참고 자료
- [ECR 할당량 및 제한](https://docs.aws.amazon.com/AmazonECR/latest/userguide/service-quotas.html)
- [AWS CLI ECR 명령어 참조](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ecr/index.html)
- [Docker Compose 설정 가이드](https://docs.docker.com/compose/)