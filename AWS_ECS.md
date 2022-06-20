# ECS란?


![](https://velog.velcdn.com/images/noahshin__11/post/7d781605-5f9f-482b-a56e-45eb34ac8cf5/image.png)
- Docker는 최근 각광 받고 있는 컨테이너 기술이다.
- 하지만 Docker를 이용해 서비스를 구축 하려면 여러가지 고려 해야할 사항이 많다.
- 따라서 필연적으로 컨테이너를 적절하게 배치하고 관리할 수 있게 도와주는 컨테이너 오케스트레이션 도구의 필요성을 느끼게 된다.
- AWS의 ECS는 Amazon에서 제공하는 '완전관리형 컨테이너 오케스트레이션 툴'로써,
- Docker 컨테이너를 이용하여 인프라 환경을 좀 더 편리하게 운영,관리 할 수 있게 해주는 서비스이다.
- 비슷한 툴로서는 Kubernetes나 Docker Swarm이 있다.

# ECS 구성 요소

ECS는 크게 아래와 같은 컴포넌트들로 구성 되어 있다.

>- Task definition
- Task
- Service
- Container Instance
- Cluster

먼저 이것들에 대해 하나하나 알아보자

## Task / Service 관계

![](https://velog.velcdn.com/images/noahshin__11/post/a26ec597-153e-4046-b0c8-cff3b6f48edf/image.png)
## 1) Task definition

#### 원하는 Docker 컨테이너를 생성할 때, 어떤 설정으로 몇 개 이상 생성 할 지를 정의한 Set 이다.

- 컨테이너의 이미지, CPU/메모리 리소스 할당 설정, port 매핑, volume 설정 같은 것들이 포함되며, 기존 docker run 명령에서 가능했던 대부분 옵션이 설정 가능하다.
- 컨테이너 오케스트레이션에서는 컨테이너가 필요에 따라서 자동적으로 실행되거나 종료될 수 있다.
- 따라서 매번 이러한 설정들을 지정하기보다는, 미리 설정들의 집합을 하나의 단위로 정의해놓고 사용한다.
- 이 단위가 바로 태스크 디피니션이다.
- 한 번 태스크 디피니션을 만들면 이 태스크 디피니션을 기반으로 특정 설정을 변경할 수 있다.

- Task definition 에는 한 개 이상의 컨테이너에 대해 정의가 가능하며, Task definition 내부에 정의된 컨테이너 사이는 link 설정으로 연결이 가능하다.
#### - Task definition 에서 정의된 대로 실제 생성된 container set 들을 Task 라고 부르게 된다.

## 2) Task

#### Task definition 에서 정의된 대로 배포된 Container Set을 Task라고 부른다.
- 즉, Task 안에는 한 개 이상의 컨테이너들이 포함되어 있으며 **ECS에서 컨테이너를 실행하는 최소 단위는 Task이다.**
- Task는 Cluster에 속한 Container instance(EC2 Instance)에 배포되게 된다.
- 또한, Task는 여러 Container instance(EC2 Instace)에 배포 가능하다.


## 3) Service

- Task 들의 Life cycle 을 관리하는 부분을 **Service** 라고 한다.
- 각 Task 들은 각자 다른 서비스이다.
- Task 를 Cluster에 몇 개나 배포할 것인지 결정하고, 실제 Task 들을 외부에 서비스 하기 위해 ELB 에 연동 되는 부분을 관리하게 된다.
- 만약 실행 중인 Task 가 어떤 이유로 작동이 중지 되면 이것을 자동으로 감지해 새로운 Task를 Cluster에 배포 하는 고가용성에 대한 정책도 Service 에서 관리한다.
#### 즉, 오토스케일링과 로드밸런싱을 관리하는 역할이다.


## 4) Container Instance (EC2 instance)

- ECS 는 Container 배포(Task 배포)를 EC2 instance 기반에 올리도록 설계 되어 있다.
- Task를 올리기 위해 등록된 EC2 instance 를 Container Instance 라고 부른다.
- ECS를 처음 시작하면 생성되는 Default Cluster 에는 Container instance를 자동으로 할당 시켜 주기도 하지만
 - 새롭게 Cluster 를 생성하게 되면 직접 Container instance 를 만들어야 한다.
- Container instance 용 AMI 이미지는 AWS 측에서 제공해 주기 때문에 어렵지 않게 생성이 가능하다.
- 하나의 Container Instace 내부에는 각각의 다른 Task가 여러 개 있을 수 있다.

## 5) Cluster

- Task 가 배포되는 Container Instance 들은 논리적인 그룹으로 묶이게 되는데 이 단위를 Cluster 라고 부른다.
- Task 를 배포하기 위한 instance 는 반드시 Cluster 에 등록되어야 한다
![](https://velog.velcdn.com/images/noahshin__11/post/997ccbe8-d1b1-4ae9-8a2c-62417751542d/image.png)


# ECS의 구조적 특징 및 주의사항


### 1) ECS의 Docker host 역할은 EC2 Instance가 담당한다.

- 컨테이너는 보통 물리 서버에 Docker daemon 을 설치하고, 그 위에 컨테이너를 하나씩 배포하게 되는데, Hypervisor 기반에서 동작되는 VM 보다 훨씬 더 많은 서비스를 올릴 수 있는게 큰 장점 중 하나이다.
- 하지만, AWS는 ECS를 위한 container 전용 서버팜을 따로 구성하지 않고, VM 형태의 EC2 instance 에서 container 를 동작시키는 방법을 택했다.

- 이유는 무엇일까?

**- AWS의 전체 서비스 설계를 보면 EC2 instance를 중심으로 다양한 서비스들이 연계되는 모양새를 갖추고 있다. 이는 AWS의 가장 큰 장점이기도 하다.**

- Network 서비스인 VPC와 ELB, EIP 에서 부터 Auto scaling, Cloud Formation 등 수십가지의 서비스가 조직적으로 엮여 있으며 이 중심에는 사용자들이 가장 많이 사용하는 EC2 instance 가 있다.

- 만약, AWS가 ECS 서비스를 위해 새로운 Container 서버팜 환경을 구성하고 설계했다면, 기존 EC2 를 중심으로 한 다양한 서비스들과의 연계에 대하여 굉장히 많은 고민을 할 수 밖에 없을 것이다.

- 차라리 컨테이너를 EC2 instance에 올림으로서, EC2 중심으로 엮여 있는 기존의 서비스를 이용하는 장점을 흡수하는게 훨씬 이득이 많았을 거라 생각한다.


### 2) Overlay network가 아닌 ELB를 이용한 Network 구조

- Docker 를 사용하려는 사람들이 늘어나면서 컨테이너를 대규모로 운영하려는 시도가 점점 많아지고, 자연스럽게 다수의 Docker host 를 clustering 형태로 통합 관리하려는 필요성이 커지게 되었다.

- host 에 container 를 어떻게 배포할 것인지에 대한 스케줄링 관련 문제, 다른 host에 배포된 container 사이의 통신 문제, 동일 Port 를 사용 하는 컨테이너들을 동시 수용할때의 host 포트 맵핑 이슈 등이 대표적인 문제들이다.
- 최근 주목 받고 있는 컨테이너 orchestration 시스템인 Kubernetes나 Docker swarm의 경우 위와 같은 이슈를 해결하기 위해 overlay network 구조를 추가해 이런 문제를 해결하고 있다.

- host간 네트워크를 논리적으로 묶어서 연결해 주는 overlay network 는 컨테이너 orchestration 을 위한 중요 구성 요소로 고려되고 있는 추세이다.


### 그렇다면 ECS 의 host clustering 을 위한 network 구조는 어떠할까.

**ECS의 Cluster 는 매우 단순하다.
**
- Cluster는 docker host 의 논리적 묶음일 뿐이지, 이를 위해 추가적인 network 설계 구조가 들어가지 않은 것으로 보인다.

- Overlay network 가 없다면, 다른 host에 상주한 컨테이너 사이에는 컨테이너에 할당된 Private IP 를 이용해 native 하게 통신하게 어려우며, 대신 외부 통신과 동일하게 각 host IP로 컨테이너에 맵핑된 port 를 이용하는 방법 밖엔 없다.

- 이는 컨테이너간 통신을 빈번히 요구 하는 application 을 올리는 경우에 상당히 어려움을 겪을 수 있다.
host 사이를 논리적으로 묶어줄 network 구조가 없다면, 결국 컨테이너 사이 통신을 위해서는 각 컨테이너가 상주한 host의 정보를 추가로 알아야만 통신이 가능하기 때문이다.

![](https://velog.velcdn.com/images/noahshin__11/post/f3f28363-949d-4e39-b486-34a9b5fc4ad5/image.png)
- ECS는 service 내부에 여러개의 task 가 배포될때 ELB 의 밸런싱 그룹에 task 들이 자동으로 들어가도록 설계되어 있다.

- 즉, service 사이의 통신은 ELB를 통해 통신을 함으로서 task의 life cycle 을 유연하게 관리할 수 있게 만든 구조이다.

- 물론, 같은 service 에 속한 task 들이 다른 host에 있게 된다면 이들 사이의 통신은 여전히 어렵다는 한계는 있지만, 굳이 동일한 service 안에 동일한 task 끼리 통신할 일은 많지 않을 것이기에 큰 문제는 없을 것으로 보인다.

- ( 보통 web 서버가 was 나 DB 와 통신하지, 굳이 동일한 일을 하는 web 서버 끼리는 통신을 주고 받을 일이 없다)



### 3) 하나의 Host에는 한 개의 Task만 수용 가능하다.

- 사실 ECS에서 가장 안타까운 점이라고 생각한다.

- 컨테이너가 host의 특정 port 를 점유 해야만 하는 구조는, 결국 같은 port로 서비스 하려는 컨테이너가 한 host 에 한 개 밖에 올라갈 수 없다는 의미이다.

- ECS 구조적 측면에서 보면, service 안에 task 를 올릴수 있는 최대 숫자는 cluster에 등록된 container instance 개수 이상이 될 수 없다는 것이다.

- 예를 들어, service 안에 apache 컨테이너가 정의된 task 를 2개 올리고 싶다면, 반드시 host 는 두대 이상이 필요하다는 말이다.
- 이는 컨테이너 집적도가 그만큼 낮아질 수 밖에 없는 구조적 한계이다.

- Kubernetes 의 경우 Overlay network 과 Pods / Service 라는 논리적 개념으로 이러한 문제를 해결한 것과 비교하면 아쉬운 부분임이 분명하다.

- 대신 host 마다 다른 task 들이 공존하도록 만들어 집적도를 올리는 방법이 그나마 단점을 상쇄하는 방법으로 보인다.

- 아래와 같이 Container instance 는 두대이지만, 각각 다른 포트를 사용하는 service를 여러 개 올려 컨테이너 집적도를 높일 수 있다.

![](https://velog.velcdn.com/images/noahshin__11/post/cd6de3af-efb4-4f62-835c-c03436940b98/image.png)

# 4) ECS 특성에 맞는 Application 설계가 필요하다.

ECS 기반으로 Application을 잘 운영하려면, Task 와 Service 개념의 구조적 특징을 이해하고 Application을 설계 해야 한다.

아래는 ECS 설계적 관점에 따른 몇가지 유의 사항이다.

## 4-1) Task definition 에는 단일 Tier 속성의 컨테이너만 정의하는게 유리하다.

- 사실 Task 에는 여러 컨테이너를 정의할 수 있다.
- 하지만, 최소 배포 단위가 Task 단위 이므로 단일한 속성의 컨테이너 하나만 정의하는 게 좋은 설계 방향일 것이다.
- 예를 들어, Task definition 에 Web / WAS / DB 컨테이너를 한꺼번에 정의했다고 가정해 보자.
- 만약 task 가 하나인 상황에서는 큰 문제가 없을것이다. 하지만 task 를 추가로 늘리려고 한다면, task에 속한 Web / WAS / DB 셋이 한꺼번에 늘어나게 된다.
- 보통 서비스에 부하가 일어나면, 각 tier 별로 어떤 구간에서 스트레스를 받는지 판단 후 해당 tier 의 서버만 추가하여 LB에 넣는 방식으로 해결한다. ( = Scale-out )
- 하지만, task에 모든 tier 를 혼합시켜 구성하면 scale-out 형태의 증설은 불가능할 것이다.
- 따라서, task 의 Scale-out 구조를 고려하여 task 를 tier 단위로 분리해서 정의하는게 좋다.

## 4-2) 용도에 맞게 Container Instance 를 VPC로 구분해 사용하면 보안성을 높일 수 있다.

- 컨테이너를 올릴 host 는 EC2 를 사용함으로 EC2 에 적용되는 다양한 서비스들을 그대로 이용할 수 있다.
- 따라서 VPC 와 같은 기능을 이용하여, 컨테이너가 올라가게 될 EC2 를 구분해 사용한다면 더욱 보안성을 높일 수 있다.
- 예를 들어, Cluster A 는 외부로 노출할 Web 컨테이너들을 배포하고, Cluster B 에는 외부 노출을 하지 않는 WAS 컨테이너를 배포한다고 가정해 보자.
- 이때, Cluster A 에 속하는 EC2 instance 들은 VPC의 public subnet 에 속하도록 하고, Cluster B 에 속하는 EC2 instance 들은 VPC 의 private subnet 에 속하도록 구성할 수 있다.
- 단, private subnet 에 배포된 EC2 instance 들은 외부 통신과 단절되게 되므로, ECS 를 사용하려면 NAT Gateway 와 같은 서비스를 이용해 outbound 트래픽에 대한 처리를 추가해 주어야 한다.


## 4-3) 컨테이너 특성상 DB 와 같은 stateful 한 서버는 컨테이너 보다는 EC2 나 RDS 를 이용해 연계해서 사용한다.

- 컨테이너는 기존의 인프라 방식에 비해 생성과 소멸에 좀 더 유연하다.
- 바꿔 말하면 컨테이너는 언제든 삭제 될 수 있음을 전제로 해야 한다.
- 그러므로 data store 가 목적인 DB 는 컨테이너의 컨셉과는 상반된 형태임을 고려해야한다.
- 따라서, 컨테이너의 특성상 stateless 한 성격의 web 이나 was 서버에 이용하기를 권장한다.
- DB 의 경우 AWS의 RDS 와같은 서비스를 이용해 컨테이너와 함께 쓴다면 안전하면서도 유연한 인프라 구성을 갖출 수 있을 것으로 생각된다.


## 4-4) ECR 과 같은 컨테이너 서비스들과 연계하여 사용하면 좋다.

- AWS 에는 ECS 말고도 컨테이너 이미지 Repository 서비스인 ECR 도 서비스 하고 있다.
- 이를 ECS 와 엮어서 사용한다면 더욱 좋을 것으로 생각 된다.



[진짜 우노님 감사합니다...](https://wooono.tistory.com/133)




