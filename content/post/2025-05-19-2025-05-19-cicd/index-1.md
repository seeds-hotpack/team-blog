---
title: CICD
description: 소프트웨어 개발과 배포를 자동화하여 효율성과 안정성을 높이기 위한 일련의 프로세스
date: 2025-05-19T23:22:01+09:00
author: ""
categories:
  - CICD
draft: false
---
## 1. CI (지속적 통합, Continuous Integration)

목표: 코드 변경사항을 자주, 지속적으로 통합하여 버그를 조기에 발견하고 개발 속도를 높이는 것.

주요 특징:

* 개발자들이 변경한 코드를 **공통 저장소(Git 등)**에 자주 푸시
* 푸시할 때마다 자동으로 테스트와 빌드가 실행됨
* 코드 충돌, 버그를 빠르게 탐지 가능

## 2. CD (지속적 전달 / 지속적 배포)

(1) 지속적 전달, Continuous Delivery)

* CI 이후에 운영 직전까지 자동화
* 빌드, 테스트, 패키징, 스테이징 서버 배포까지 자동 수행
* 최종 배포만 수동 (예: 운영자가 “배포” 버튼 누름)

(2) 지속적 배포, Continuous Deployment)

* 위 과정을 운영 서버까지 자동 배포
* 코드 푸시만 하면 최종 사용자에게 자동 제공됨
* 테스트 통과 시 수동 개입 없이 배포 완료

## 3. Spring Boot 백엔드 프로젝트를 기준으로 GitHub Actions + Jenkins를 활용한 CI/CD 파이프라인 구성 예시

1. 전체 구조 요약

```text
[개발자 Git Push]
      ↓
[GitHub Actions (CI)]
  - Gradle 빌드
  - 테스트 실행
  - Docker 이미지 생성
  - 이미지 → GitHub Container Registry(GHCR)에 푸시
      ↓
[Jenkins (CD)]
  - GHCR에서 Docker 이미지 pull
  - 컨테이너 실행
  - EC2 운영 서버에 자동 배포
```

2. GitHub Actions 설정 (.github/workflows/ci.yml)

```yaml
name: Spring CI/CD Pipeline

on:
  push:
    branches: [ "main" ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout
      uses: actions/checkout@v3

    - name: Set up JDK
      uses: actions/setup-java@v3
      with:
        distribution: 'temurin'
        java-version: '17'

    - name: Build with Gradle
      run: ./gradlew clean build

    - name: Docker Login to GHCR
      run: echo "${{ secrets.GHCR_TOKEN }}" | docker login ghcr.io -u ${{ github.actor }} --password-stdin

    - name: Build Docker image
      run: docker build -t ghcr.io/your-username/your-app-name:latest .

    - name: Push Docker image to GHCR
      run: docker push ghcr.io/your-username/your-app-name:latest
```

* GHCR_TOKEN은 GitHub Personal Access Token으로 secrets에 등록해둬야 한다.

3. Jenkins 설정 (CD 단계)

* Jenkins에서 파이프라인 생성
* 다음과 같은 pipeline script 사용:

```groovy
pipeline {
    agent any

    stages {
        stage('Pull Docker Image') {
            steps {
                sh 'docker login ghcr.io -u your-username -p your-token'
                sh 'docker pull ghcr.io/your-username/your-app-name:latest'
            }
        }
        stage('Stop & Remove Old Container') {
            steps {
                sh 'docker stop spring-app || true'
                sh 'docker rm spring-app || true'
            }
        }
        stage('Run New Container') {
            steps {
                sh 'docker run -d --name spring-app -p 8080:8080 ghcr.io/your-username/your-app-name:latest'
            }
        }
    }
}
```

4. 결과

* GitHub에 main 브랜치로 코드를 푸시하면:
* GitHub Actions가 자동으로 빌드 → 이미지 생성 → GHCR에 푸시
* Jenkins가 감지하여 GHCR에서 pull → 도커 컨테이너 재시작 → 운영 서버 자동 반영
