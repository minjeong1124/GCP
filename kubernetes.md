- **Linux Container :** 
가상화는 단일 하드웨어 시스템에서 여러 운영체제 동시 실행하는 반면, 컨테이너는 동일한 운영체제 커널을 공유하며 시스템의 나머지 부분으로부터 애플리케이션 프로세스를 격리.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b47072a1-1797-437e-b415-3ebbcfd7a216/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b47072a1-1797-437e-b415-3ebbcfd7a216/Untitled.png)

- **Docker :** 
Linux 컨테이너를 만들고 사용할 수 있도록 하는 컨테이너 화 기술, 컨테이너 툴.
여러 프로세스와 애플리케이션을 서로 개별적으로 실행하여 인프라를 더 효과적으로 활용하고, 개별 시스템을 사용할 때에 보안이 그대로 유지됨. 
이미지 기반 배포 모델을 제공 하므로 여러 환경 전반에서 애플리케이션을 모든 종속 항목과 손쉽게 공유 할 수 있다. Linux Containcer 기반으로 구축 되었지만, 사실은 미세하게 다르다. LXC는 경량의 가상화 방법으로 유용하게 사용되었지만 한계 있었고, Docker 기술은 컨테이너를 실행하는 기능 이상의 것을 제공하며 무엇보다도 컨테이너 생성 및 구축, 이미지 전송, 이미지 버전 관리 프로세스 용이.

이미지는 컨테이너 실행에 필요한 파일과 설정값등을 포함하고 있는 것으로 상태값을 가지지 않고 변하지 않습니다(Immutable). 컨테이너는 이미지를 실행한 상태라고 볼 수 있고 추가되거나 변하는 값은 컨테이너에 저장됩니다. 같은 이미지에서 여러개의 컨테이너를 생성할 수 있고 컨테이너의 상태가 바뀌거나 컨테이너가 삭제되더라도 이미지는 변하지 않고 그대로 남아있습니다.
말그대로 이미지는 컨테이너를 실행하기 위한 모오오오오든 정보를 가지고 있기 때문에 더 이상 의존성 파일을 컴파일하고 이것저것 설치할 필요가 없습니다. 이제 새로운 서버가 추가되면 미리 만들어 놓은 이미지를 다운받고 컨테이너를 생성만 하면 됩니다. 한 서버에 여러개의 컨테이너를 실행할 수 있고, 수십, 수백, 수천대의 서버도 문제없습니다.

    ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/229697b8-f497-45bf-952e-ca55a6df3583/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/229697b8-f497-45bf-952e-ca55a6df3583/Untitled.png)

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/48add512-417b-4628-a011-db0f1e7e0922/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/48add512-417b-4628-a011-db0f1e7e0922/Untitled.png)

도커 이미지는 컨테이너를 실행하기 위한 모든 정보를 가지고 있기 때문에 보통 용량이 수백메가MB에 이릅니다. 처음 이미지를 다운받을 땐 크게 부담이 안되지만 기존 이미지에 파일 하나 추가했다고 수백메가를 다시 다운받는다면 매우 비효율적일 수 밖에 없습니다.

도커는 이런 문제를 해결하기 위해 **레이어(layer)**라는 개념을 사용하고 여러개의 레이어를 하나의 파일시스템으로 사용할 수 있게 해줍니다. 이미지는 여러개의 읽기 전용(read only) 레이어로 구성되고 파일이 추가되거나 수정되면 새로운 레이어가 생성됩니다.

### **Dockerfile 형식**

`FROM ubuntu:14.04
MAINTAINER Foo Bar <foo@bar.com>
RUN apt-get update
RUN apt-get install -y nginx
RUN echo "\ndaemon off;" >> /etc/nginx/nginx.conf
RUN chown -R www-data:www-data /var/lib/nginx
EXPOSE 8080
WORKDIR /etc/nginx
CMD ["nginx"]
COPY app.js`

- `FROM` : 어떤 이미지를 기반으로 할지 설정
- `MAINTAINER` : 메인테이너 정보
- `RUN` : 쉘 스크립트 혹은 명령 실행
    - 이미지 생성 중에는 사용자 입력을 받을 수 있어서, apt-get install에서 `-y` 옵션을 꼭 넣어줘야 함. 안 넣으면 Fail
    - `RUN`은 한줄이 이미지 하나로 빌드됨
- `EXPOSE` : 포트 노출
- `CMD` : 컨테이너가 시작되었을 때 실행할 실행 파일 또는 쉘 스크립트
- `WORKDIR` : CMD에서 설정한 실행 파일이 실행될 디렉터리
- `COPY` : 말 그대로 복사

- **Container Orchestration :**
애플리케이션은 특정 애플리케이션이 설계된 대로 기능 하도록 함께 작동해야 하는 수십 또는 수백 개의 느슨하게 결합된 컨테이너 구성 요소로 구성. 컨테이너 오케스트레이션은 개별 구성 요소와 애플리케이션 계층의 작업을 정리하는 과정. 여러 개의 서버에 컨테이너를 배포하고 운영하면서 서비스 간 연결을 쉽게 해주는 것. 서버들을 하나로 묶어 적당한 서버를 자동으로 선택해 애플리케이션을 배포하고 부하가 생기면 컨테이너를 늘리고 일부 서버에 장애가 발생하면 정상 동작 중인 서버에 다시 띄워 장애를 방지.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6a1e2473-d0f7-46c6-8ab9-80ffafb7c0bf/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6a1e2473-d0f7-46c6-8ab9-80ffafb7c0bf/Untitled.png)

- **Kubernetes :**
Linux 컨테이너 작업을 자동화하는 오픈소스 플랫폼.
이 플랫폼에서는 컨테이너 화 된 애플리케이션을 배포하고 확장하는 데 수동 프로세스가 필요하지 않다. 즉, Linux 컨테이너를 실행하는 호스트 그룹을 함께 클러스터링 할 수 있으며 쿠버네티스를 통해 이러한 클러스터를 쉽고 효율적으로 관리할 수 있다. 
실제 프로덕션 애플리케이션은 여러 컨테이너에 걸쳐 있으며 이러한 컨테이너는 여러 서버 호스트에 배포되어야 한다. 쿠버네티스는 규모에 맞는 컨테이너를 배포하는 데 필요한 오케스트레이션 및 관리 기능 제공.

`kubectl : 쿠버네티스 명령줄 설정 툴

master : 관리자만 접속할 수 있도록 보안 설정을 해야 하고 마스터 서버가 죽으면 클러스터를 관리할 수 없기 때문에 보통 3대를 구성하여 안정성을 높입니다. 개발 환경이나 소규모 환경에선 마스터와 노드를 분리하지 않고 같은 서버에 구성하기도 합니다.

node : 노드 서버는 마스터 서버와 통신하면서 필요한 Pod을 생성하고 네트워크와 볼륨을 설정합니다. 실제 컨테이너들이 생성되는 곳으로 수백, 수천대로 확장할 수 있습니다. 각각의 서버에 라벨을 붙여 사용목적(GPU 특화, SSD 서버 등)을 정의할 수 있습니다.

pod : 배포할 수 있는 가장 작은 단위로 한 개 이상의 컨테이너와 스토리지, 네트워크 속성. Pod에 속한 컨테이너는 스토리지와 네트워크를 공유하고 서로 localhost로 접근 가능. 컨테이너를 하나만 사용하는 경우도 반드시 Pod으로 감싸서 관리.`

    ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8ea2107e-5c36-4334-8072-2e69040ef4a1/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8ea2107e-5c36-4334-8072-2e69040ef4a1/Untitled.png)

    `ReplicaSet : Pod을 여러개 복제하여 관리하는 오브젝트. Pod을 생성하고 갯수를 유지하려면 ReplicaSet을 사용해야 함.` 

    ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9023de89-1f77-467c-86d8-cb7eefdc22a8/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9023de89-1f77-467c-86d8-cb7eefdc22a8/Untitled.png)

    `kubelet : 노드에 할당된 Pod의 생명주기를 관리합니다. Pod을 생성하고 Pod 안의 컨테이너에 이상이 없는지 확인하면서 주기적으로 마스터에 상태를 전달합니다. API 서버의 요청을 받아 컨테이너의 로그를 전달하거나 특정 명령을 대신 수행하기도 합니다.`

    kubectl → master kubernetes가 node에 대해 pod를 예약하면 해당 node의 kubelet이 지정된 컨테이너를 실행하도록 Docker에 명령 → kubelet은 Docker로부터 이러한 컨테이너의 상태를 계속해서 수집하고 master에서 해당 정보를 집계 → Docker는 해당 node에 컨테이너를 pulling하고 이러한 node를 평소와 같이 시작하고 중지 (차이가 있다면 관리자가 모든 컨테이너의 모든 노드에서 작업을 직접 수행하는 것이 아니라 자동화된 시스템이 이러한 작업을 Docker에 요청한다는 것)

    자세한 글 : [https://subicura.com/2019/05/19/kubernetes-basic-1.html](https://subicura.com/2019/05/19/kubernetes-basic-1.html)

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6660f781-126e-4764-b711-cde284132762/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6660f781-126e-4764-b711-cde284132762/Untitled.png)

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3e72e265-2d3e-4fb4-ba3d-00ecc252b181/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3e72e265-2d3e-4fb4-ba3d-00ecc252b181/Untitled.png)

쿠버네티스에 의해서 배포 및 관리되는 가장 기본적인 오브젝트는 컨테이너화되어 배포되는 애플리케이션의 워크로드를 기술하는 오브젝트로 Pod, Service, Volume, Namespace 4가지가 있다.

Pod는 컨테이너 화 된 애플리케이션, Volume은 디스크, Service는 로드 밸런서 그리고 Namespace는 패키지 명? 

### Pod

Pod 는 쿠버네티스에서 가장 기본적인 배포 단위로, 컨테이너를 포함하는 단위이다.

쿠버네티스의 특징중의 하나는 컨테이너를 개별적으로 하나씩 배포하는 것이 아니라 Pod 라는 단위로 배포하는데, Pod는 하나 이상의 컨테이너를 포함한다.

### Service

Pod와 볼륨을 이용하여, 컨테이너들을 정의한 후에, Pod 를 서비스로 제공할때, 일반적인 분산환경에서는 하나의 Pod로 서비스 하는 경우는 드물고, 여러개의 Pod를 서비스하면서, 이를 로드밸런서를 이용해서 하나의 IP와 포트로 묶어서 서비스를 제공한다.

Pod의 경우에는 동적으로 생성이 되고, 장애가 생기면 자동으로 리스타트 되면서 그 IP가 바뀌기 때문에, 로드밸런서에서 Pod의 목록을 지정할 때는 IP주소를 이용하는 것은 어렵다. 또한 오토 스케일링으로 인하여 Pod 가 동적으로 추가 또는 삭제되기 때문에, 이렇게 추가/삭제된 Pod 목록을 로드밸런서가 유연하게 선택해 줘야 한다.

그래서 사용하는 것이 라벨(label)과 라벨 셀렉터(label selector) 라는 개념이다.

서비스를 정의할때, 어떤 Pod를 서비스로 묶을 것인지를 정의하는데, 이를 **라벨 셀렉터**라고 한다. 각 Pod를 생성할 때 메타데이타 정보 부분에 라벨을 정의할 수 있다. 서비스는 라벨 셀렉터에서 특정 라벨을 가지고 있는 Pod만 선택하여 서비스에 묶게 된다.

- **GCE vs GKE :**

GKE 사용법 :

[https://medium.com/@jwlee98/gcp-gke-차근-차근-알아보기-1탄-gke-개요-382dc69b2ec4](https://medium.com/@jwlee98/gcp-gke-%EC%B0%A8%EA%B7%BC-%EC%B0%A8%EA%B7%BC-%EC%95%8C%EC%95%84%EB%B3%B4%EA%B8%B0-1%ED%83%84-gke-%EA%B0%9C%EC%9A%94-382dc69b2ec4)
