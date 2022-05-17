# Fargate란?
> AWS Fargate는 기본 인프라를 관리할 필요 없이 컨테이너를 배포하고 관리할 수 있는 컴퓨팅 엔진
=> 컨테이너만 올리면 분산처리하여 서버가 꺼지지 않게 배포를 도와준다.


하러 가기 이전에 파게이트는 ecs의 기본적인 개념을 알 필요가 있는데

# ECS
클러스터에서 컨테이너를 쉽게 실행, 중지 및 관리할 수 있게 해주는 확장성과 속도가 뛰어난 컨테이너 관리 서비스


## Cluster
![](https://velog.velcdn.com/images/noahshin__11/post/dd3f37b4-ce34-4259-9d69-b440b0396f23/image.png)
 ecs에서 서비스, 작업정의가 작업을 하는 논리적 공간, 그룹이다
 
 
 
## Task definition
- 작업정의는 ecs에서 최소 실행 단위
- 태스크를 실행을 위한 설정과, 컨테이너에 대한 정보를 포함하고 있다.
- 클러스터에 종속되어 있지 않다.
- 작업정의를 선언하면 다른 클러스터에서 똑같이 생성을 할 수 있다.

## Task
- 컨테이너를 실행하는 최소의 단위이며 서비스에 의하여 실행이 됩니다.
- 클러스터 안에 여러 작업을 가진다.
![](https://velog.velcdn.com/images/noahshin__11/post/e02e11b1-1017-427c-b7f0-8ef8c3fa3712/image.png)
해당 그림처럼 작업정의는 따로 선언을 하고, 필요한 클러스터 안에서 선언된 작업정의를 가져와 작업을 실행 합니다.

## Service
서비스는 작업정의를 이용해, 태스크의 작업개수, 배포방식 등등 여러 작업에 대한 설정들을 가져와 관리를 한다.

## Container Instance
Docker deamon과 ECS agent가 실행 중인 EC2 인스턴스이다.



참조: https://velog.io/@chory/Fargate-%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0-1