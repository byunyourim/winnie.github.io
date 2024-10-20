---
layout: post
title: Docker
feature-img: "assets/img/portfolio/docker_logo2.png"
img: "assets/img/portfolio/docker_logo3.png"
date: 2024-08-15
---
![kbo.png](https://github.com/user-attachments/assets/97bf612b-e1a1-4eab-b288-526aa8764f6b)
  


## Docker
컨테이너 환경에서 응용 프로그램과 종속성을 컨테이너로 패키징화하여 실행하는 기술
이를 통해 일관되게 실행할 수 있고, 개발 환경과 운영 환경 사이의 차이로 인한 문제를 줄일 수 있다.

도커 컨테이너는 가볍고 빠르며 확장성이 좋아서 개발 및 배포 프로세스를 간소화하는데 사용된다.

따라서 Docker는 어플리케이션과 필요한 모든 파일을 하나의 박스(컨테이너)에 담아 
**언제 어디서나 동일하게 실행**될 수 있도록 도와주는 도구이다.



<br>

![docker_process.png](https://github.com/user-attachments/assets/5806e7c0-6d83-4b66-aae7-e770a068794d)
#### Dockerfile
- 텍스트 파일로, Docker 이미지를 만드는 방법을 정의한 스크립트
- 어떤 운영체제, 소프트웨어, 명령을 사용할지 등이 포함된다. 


#### Docker Image
- Dockerfile을 바탕으로 Docker Image를 생성한다.
- 이미지는 컨테이너를 만들기 위한 모든 구성 요소를 포함한 템플릿이다.
- 컨테이너를 생성할 때 사용합니다.
- 레이어로 구성될 수 있으며, 여러 레이어가 쌓여 하나의 이미지를 구성한.




#### Docker Container
- Docker이미지를 사용하여 실제로 실행되는 컨테이를 생성한다.
- 컨테이너는 독립적인 환경에서 실행되며, 동일한 이미지로 여러 개의 컨테이너를 생성할 수 있다.



![hub.png](https://github.com/user-attachments/assets/0220cde2-548d-42a3-89da-7bd006f86c47)
#### Docker Hub
- 도커 이미지는 Docker Hub에 업로드될 수 있으며, 이미지를 공유한다.
- Docker Hub에 있는 이미지를 가져와 직접 사용하거나, 수정하여 사용할 수 있다.




> #### Docker 장단점
> - **일관된 개발 환경** : 어디서 실행하든 동일하게 작동한다.
> - **신속한 배포 및 확장** : 새로운 서버나 인스턴스에 빠르게 배포할 수 있다.
> - **효율적인 리소스 사용** : VM에 비해 경량이며, 컨테이너 간에 OS커널을 공유하여 리소스 소비가 적다.
> - **이식성** : 도커 이미지는 어디서나 동일하게 동작하며, 다양한 환경에서 쉽게 이식할 수 있다.
> - **버전 관리** : 태그를 이용하여 버전을 관리할 수 있다.