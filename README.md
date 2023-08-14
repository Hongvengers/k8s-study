# k8s-study
쿠버네티스 학습을 위한 레파지토리



- What is k8s
> Kubernetes is a portable, extensible, open source platform for managing containerized workloads and services, that facilitates both declarative configuration and automation.

_쿠버네티스 공식문서 발췌_<br>

쿠버네티스란 컨테이너화된 workloads, services를 관리하기 위한 어디서나 쓸 수 있고, 확장가능한 오픈소스 플랫폼이다. <br>
쿠버네티스는 선언적(Declarative) 설정과 자동화를 용이하게 해준다.<br>
<br>  
- 유래? <br>
Kubernetes란 조타수(helmsman)를 뜻하는 그리스어에서 유래되었다. <br>
아마 Container를 실은 배를 운행한다는 관점에서 지어진 네이밍같다. <br>
<br>

- Structure of k8s

![image](https://github.com/Hongvengers/k8s-study/assets/66003338/3b85150c-aef5-4a98-b55c-0445132ec2a6)

쿠버네티스를 배포하게되면 쿠버네티스의 리소스를 관리할 수 있는 머신들의 집합인 쿠버네티스 클러스터(k8s cluster)를 얻는다.<br>
쿠버네티스 클러스터의 구성에는 크게 Control Plane과 Node 두 개의 구성요소로 나뉘어진다.<br>
<br>
Control Plane은 사용자가 선언한(Declared) 명세를 스케쥴링 및 조작, 이벤트 수행 등을 통해 쿠버네티스 클러스터의 상태를 계속해서 조정해 주는 관제탑과 같은 역할을 한다. <br>
따라서, Control Plane은 우리의 서비스가 올라가는 영역과는 별개로 쿠버네티스 인프라가 정상적으로 동작하는 것을 보장해주는 친구이다. 이에 포함되는 컴포넌트는 다음과 같다. <br>
- API Server <br>
  - Control Plane의 프론트 데스크다. kubectl 와 같은 명령어를 통해 외부로 API를 노출하여 k8s cluster로의 조작을 할 수 있도록 제공해준다. <br>
- etcd <br>
  - k8s cluster의 데이터를 담는 쿠버네티스의 key-value 저장소이다. <br>
- (Cloud) Controller Manager <br>
  - k8s cluster의 상태를 감시하고 선언한 상태(spec)와 현재 상태(status)가 일치하도록 지속 관리를 해준다.(컨트롤 루프) <br>
  - Controller Manager엔 다양한 Controller가 속해있음 <br>
    - Node 상태 모니터링 <br>
    - Deployment, ReplicaSet, StatefulSet 등의 리소스 상태 관리 및 Pod 생성 및 관리 <br>
- Scheduler <br>
  - k8s cluster에 속해있는 여러 Node 중 최적의 Node를 판단하여 아직 할당되지 않은 Pod를 Node에 할당하는 스케쥴링 작업을 행함. <br>
  - 최적의 Node는 어떻게 판단하는가?  <br>
    - Affinity 및 Taint 등의 제약 사항 설정을 기반으로 판단 <br>
    - 각 Node의 kubelet이 수집한 데이터를 기반으로 판단 <br>
<br>

Node는 우리가 배포할 실제 서비스들이 동작 하게 될 영역이다. Node의 컴포넌트들은 동작중인 Pod(Container의 집합)를 유지 및 관리하고, 서비스로의 통신을 관리하는 역할을 수행한다. 이에 포함되는 컴포넌트는 다음과 같다. <br>
- kubelet <br>
  - Node를 관리하는 관리자같은 컴포넌트이다.  <br>
  - Node 및 Pod/Container의 데이터를 수집함. (Memory, CPU 프로세스 등) <br>
  - API Server와 통신을 하며 Pod의 컨테이너들이 정상 동작하도록 관리한다. <br>
  - Control Plane에서 배포 의사 결정된 Pod들에 대해 실제로 Container Runtime에 배치 및 실행 명령을 함. <br>
- kube-proxy <br>
  - Node에서 실행되는 네트워크 프록시이며, Service 리소스의 구현부이다. <br>
  - Pod에서 실행중인 우리 어플리케이션을 외부로 노출하는 방법에 대한 프록시를 제공해준다. <br>
- Container Runtime <br>
  - 실제 Container의 실행을 담당해주는 컴포넌트이다 <br>
  - containerd, dockerd, CRI-O 와 같은 컨테이너 런타임 인터페이스(CRI)를 제공해줌. <br>
<br>

- 왜 쿠버네티스를 사용하는가? <br>
기술은 계속해서 발전해왔다. <br>
전통적인 On-Premise 애플리케이션 배포 방식. <br>
On-Premise 방식의 리소스 가용성을 해결하기 위한 VM을 통한 가상화 배포 방식. <br>
VM에서 한단계 나아가 격리 수준을 완화하여 어플리케이션 간 OS를 공유할 수 있도록 가상화된 Container 개발/배포 방식 등. <br>
위처럼 기술은 계속 발전해왔고 현재의 Container 개발 시대에 이르렀다. <br>
이제 우리는 효율적으로 가용할 수 있는 격리된 수준의 리소스를 제공하며, 가볍고 기민한 배포가 가능한 Container에 서비스를 하게 되고, 이를 효율적으로 도와주는 친구가 바로 Kubernetes이다. <br>
특징? <br>
- Auto Scaling <br>
- Self Healing <br>
- Declarative Deployment <br>
 <br>
 
- 쿠버네티스  운용 시 기본적으로 알아야하는 3대 k8s 리소스 <br>
  - 워크로드 <br>
  - 서비스 <br>
  - 인그레스
