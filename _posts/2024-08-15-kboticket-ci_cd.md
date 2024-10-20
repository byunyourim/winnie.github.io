---
layout: post
title: CI/CD Github Action
feature-img: "assets/img/portfolio/ncp_logo2.jpg"
img: "assets/img/portfolio/npc_logo1.png"
date: 2024-08-15
---

자동화된 프로세스를 통해 수동 작업을 줄이고 불필요한 시간을 줄이기 통해 CI/CD를 구축했다.  

CI/CD를 구축하는 방법에는 여러 가지 종류가 있는데, 그 중 Github Action을 선택했다. 


<br>

### Github Action
Github에서 제공하는 CI/CD 작업을 자동화하고 관리하는 도구로  PR, 푸시 등 코드 변경이 일어났을 때, 빌드 및 테스트와 배포를 자동으로 수행하도록 해준다.

Github Action은 CI/CD 템플릿을 지원하기 때문에 쉽고 빠르게 CI/CD를 구축할 수 있어서 선택하게 되었다.  



<br>

#### CI/CD
- CI는 코드를 지속적을 통합하고 자동으로 테스트하는 프로세스로, 코드를 Push하거나 PR을 생성할 때마다, 자동으로 빌드와 테스트를 진행한다.
- CD는 지속적으로 통합된 코드를 자동으로 운영 환경에 배포하는 프로세스이다.  
  
    [CI/CD 지속적 통합과 지속적 배포](https://imwinnie.com/posts/CI-CD/)




<br><br>

## CI
```yaml
name: CI

on:
  pull_request:
    branches: [ "develop" ]

jobs:
  build:

    runs-on: ubuntu-latest
    permissions:
      contents: read

    steps:
    - uses: actions/checkout@v4
    - name: Set up JDK 17
      uses: actions/setup-java@v4
      with:
        java-version: '17'
        distribution: 'temurin'

    - name: Setup Gradle
      uses: gradle/actions/setup-gradle@af1da67850ed9a4cedd57bfd976089dd991e2582 # v4.0.0
      with:
        gradle-version: '7.6'

    - name: Build with Gradle Wrapper
      run: |
        ./gradlew build --version
      shell: bash

    - name: Run Tests
      run: |
        ./gradlew test
      shell: bash
```
#### flow
develop 브랜치에 대해 PR을 생성했을 때, develop 브랜치에 대한 자동 빌드와 테스트를 실행한다.



<br>

## CD
```yaml
name: CD

on:
  push:
    branches: [ "develop" ]

jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      contents: read

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'

      - name: Setup Gradle
        uses: gradle/actions/setup-gradle@af1da67850ed9a4cedd57bfd976089dd991e2582 # v4.0.0
        with:
          gradle-version: '7.6'

      - name: Grant Execute Permission For Gradlew
        run: chmod +x gradlew

      - name: Clean & Build With Gradle
        run: ./gradlew clean build -x test

      - name: Check Docker Username
        run: echo ${{ secrets.DOCKER_USERNAME }}

      - name: Docker build & Push
        run: |
          docker login -u ${{ secrets.DOCKER_USERNAME }} -p ${{ secrets.DOCKER_PASSWD }}
          docker build -f Dockerfile -t ${{ secrets.DOCKER_USERNAME }}/ticketing-repository .
          docker push ${{ secrets.DOCKER_USERNAME }}/ticketing-repository

      - name: Deploy Images with Docker compose
        uses: appleboy/ssh-action@master
        with:
          host: ${{ secrets.SERVER_HOST }}
          username: ${{ secrets.SERVER_USERNAME }}
          password: ${{ secrets.SERVER_PASSWORD }}
          script_stop: true
          script: |
            container_ids=$(sudo docker ps -q)
            if [ -n "$container_ids" ]; then
              sudo docker stop $container_ids
              sudo docker rm $container_ids
            fi
            sudo docker pull ${{ secrets.DOCKER_USERNAME }}/ticketing-repository
            sudo docker run -d -p 8080:8080 ${{ secrets.DOCKER_USERNAME }}/ticketing-repository
            sudo docker system prune
```
CD는 develop 브랜치에 푸시 이벤트가 발생할 때 실행된다.  
  
#### flow
1. 프로젝트를 빌드한 후 도커에 로그인 하기 위한 사용자의 이름을 확인한다.  

2. 그 후 Docker Hub에 로그인하고, Dockfile을 사용하여 Docker 이미지를 빌드하고, Docker Hub에 푸시한다.  
3. SSH를 사용하여 원격 서버에 접속하여 현재 실행 중인 Docker 컨테이너를 중지하고 제거한다.   

4. 마지막으로 최신 이미지를 pull하고 새로운 컨테이너를 실행한 후 불필요한 Docker 데이터를 정리한다.  








<br><br><br>

### Github Action을 사용한 CI/CD 과정 자동화
GitHub에서 코드가 푸시되거나 PR이 생성되면 Github Action이 자동으로 실행된다.  
이 과정에서 설정된 CI/CD 파이프라인이 작동하여 빌드 및 테스트가 수행되며, 만약 테스트가 실패하는 경우 이메일로 알림을 받을 수 있다.  
  
  
개발 서버와 운영 서버의 환경이 달라, 애플리케이션이 운영체제에 종속되지 않도록 Docker로 이미지를 빌드하여 동일한 환경에서 동작할 수 있도록 하였다. 
빌드된 이미지를 Docker Hub에 업로드하고, 운영 서버의 Docker Hub에서 이미지를 다운로드하고 실행하여 CD 자동화 작업을 수행한다.  




