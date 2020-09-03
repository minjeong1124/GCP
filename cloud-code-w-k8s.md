## 왜 하는가?

1. 애플리케이션을 개발하는 IDE에 통합 되기 때문에,  코드 개발과 k8s 환경 구성을 함께 할 수 있고, 실행 및 디버깅, 로그 확인까지 가능.

- 로컬 배포 간소화 (launch) 
SkaffoId, Kubectl 등 도구를 통해 IDE에서 바로 코드 피드백 볼 수 있음, 장점이자 단점.. risk 유
- 실행 중인 애플리케이션 디버그 (attach) 
기본 제공되는 IDE 디버깅 기능을 활용해 IDE에서 코드 디버그 가능

2. skaffold와 통합되어 빌드/배포 파이프라인의 작성 및 실행이 가능.

3. Skaffold 와 Kubernetes 설정 파일(yaml) 작성 시, 기본 템플릿 제공 및 키워드 자동 완성 지원하여, 설정 파일들을 쉽게 작성할 수 있음

4. 간편한 Google Cloud API 통합
IDE에서 Google Cloud API 찾아 추가 및 구성하고 관련 문서 쉽게 열람 가능
우측 바에 Google Cloud Storage 탭 : 버킷, 폴더, 블랍 모두 볼 수 있음 
우측 바 Kubernetes Explorer 탭 : 클러스터 리소스들의 정보 확인, 로그 뷰어 및 터미널 창

**⇒ 위 장점들을 통해 프로젝트에 적용 여부 테스트**

## 어떻게 할 것인가?

이론 파악 → 개인 계정으로 클러스터 생성 및 클라우드 코드 적용 → 현재 neart 프로젝트에 클라우드 코드 적용

## 스터디한 결과

- Cloud Code 
GCP의 k8s or cloud run 기반 애플리케이션 개발 도구로, Intellij나 VS 같은 IDE(통합 개발 환경)에 플러그인 방식으로 연결해서 사용.
- 설치 
[https://cloud.google.com/code/docs/intellij/install](https://cloud.google.com/code/docs/intellij/install)

- 샘플 프로젝트 적용 방법
[https://cloud.google.com/code/docs/intellij/creating-a-k8-app](https://cloud.google.com/code/docs/intellij/creating-a-k8-app)

- 배포
- '`Run on Kubernetes`' 실행 작업 아이콘으로 이미지를 빌드하고 프로젝트를 Kubernetes 클러스터에 배포
- '`Develop on Kubernetes`' 실행 작업 아이콘으로 Kubernetes 클러스터에서 개발 주기를 시작
개발 주기가 시작되면 Cloud Code가 Skaffold를 사용하여 프로젝트의 이미지를 빌드한 다음 태그를 지정하고 구성된 저장소에 이를 푸시한 후, kubectl을 사용하여 프로젝트 Kubernetes 매니페스트를 배포

- 삽질 story ,, 주의사항 ******🚨
******
    1. 개인 계정과 회사 계정 충돌 시

    - `gcloud init` 
    → 원하는 계정으로 로그인 
    → `gcloud container clusters get-credentials 클러스터 정보`

    - IDE 우축 툴 바 Kubernetes Explorer에서 개인 계정 프로젝트의 클러스터로 전환해도 되지만 (된다고 나와있지만 어떤 이유에서인지 터미널로 확인해보니 되지 않았음) 
     [k8s 명령어 kubectl 설치 방법](https://kubernetes.io/ko/docs/tasks/tools/install-kubectl/#macos%EC%97%90%EC%84%9C-curl%EC%9D%84-%EC%82%AC%EC%9A%A9%ED%95%98%EC%97%AC-kubectl-%EB%B0%94%EC%9D%B4%EB%84%88%EB%A6%AC-%EC%84%A4%EC%B9%98) 
    터미널에서 kubectl 설치 후 `kubectl config view` 해 본 후 current context가 전환되어 있지 않다면, 다시 `kubectl config use-context 클러스터 정보` 로 cluster switch

    2. 이미지 저장소 입력 시 `{project-name}` 이 아닌 `{project-id}` 로 입력 할 것.
    프로젝트 이름이라고 문서에 되어 있었으나 계속 저장소를 찾지 못하여 id 입력하니 됨 ..

        ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b4a31558-4b5a-48a8-98ba-4f663b4f8bee/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b4a31558-4b5a-48a8-98ba-4f663b4f8bee/Untitled.png)

    3. cloud code 플러그인을 프로젝트에 사용하려면 추가 구성 필요. 추가 구성으로는 자동으로 생성되거나 제공된 템플릿으로 만들 수 있는 Skaffold YAML 파일 필요. ([yaml 문법](https://lejewk.github.io/yaml-syntax/))
    Dockerfile이 있는 프로젝트의 경우, 문서에 나와있는 대로 Tools → Cloud Code → Kubernetes → add Kubernetes support를 하면 추가 구성이 자동으로 이루어지지만, 
    현재 neart 프로젝트는 멀티 모듈로 되어 있어 Dockerfile이 root가 아닌 모듈 별로 되어 있음되지 않음 .. (root에 있어야 인식 하나 봄) ⇒ 어쩔 수 없이 수동으로 skaffold.yaml 파일 생성

⇒ skaffold 설치

`curl -Lo skaffold[https://storage.googleapis.com/skaffold/releases/v1.13.2/skaffold-darwin-amd64](https://storage.googleapis.com/skaffold/releases/v1.13.2/skaffold-darwin-amd64) && chmod +x skaffold && sudo mv skaffold /usr/local/bin`

⇒ 프로젝트 root 디렉터리에 `skaffold.yaml` 수동 만들기
참고 : [https://skaffold-latest.firebaseapp.com/docs/references/yaml/](https://skaffold-latest.firebaseapp.com/docs/references/yaml/)

```jsx
apiVersion: skaffold/v2alpha2
kind: Config
metadata:
  name: nearart-end-user-server
build:
  artifacts:
    - image: nearart-end-user-server
      docker:
        dockerfile: end-user/Dockerfile
        noCache: false
deploy:
  helm:
    releases:
      - name: nearart-end-user-server
        chartPath: end-user/charts/chart
        setValues:
          image.repository: nearart-end-user-server
```

했더니 

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8960dcf0-a35f-471b-aa70-533709a43bfa/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8960dcf0-a35f-471b-aa70-533709a43bfa/Untitled.png)

프로젝트에 적용 됨. 
하지만 뭔가 설정이 잘못돼서 삽질 중 .. 

```jsx
Listing files to watch...
 - nearart-end-user-server
watching files for artifact "nearart-end-user-server": listing files: file pattern [target/end-user-0.0.1.jar] must match at least one file
```

dockerfile 인식은 하나 경로 및 파일 찾지 못해 수정 필요
⇒ 다단계 빌드 방법 사용 (AS builder, COPY —from=builder)

```jsx
FROM openjdk:8-jdk-slim AS builder
ENV PORT 8080
'
'
'
'
'
COPY --from=builder . /opt/app.jar
'
'
'
```

[https://github.com/docker/cli/issues/1559](https://github.com/docker/cli/issues/1559)
⇒ 이미지 업로드 완료 

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7d57a9ef-0a2c-4df4-8411-83251547b886/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7d57a9ef-0a2c-4df4-8411-83251547b886/Untitled.png)

⇒ but, helm 배포 실패 (helm 설치 안되어 있어서 helm command failed)

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6b2a988b-7cbd-4b7a-a53f-e16c0c1f032a/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6b2a988b-7cbd-4b7a-a53f-e16c0c1f032a/Untitled.png)

```jsx
time="2020-09-03T00:31:14+09:00" level=warning msg="deployer cleanup: failed to determine binary version: helm version command failed \"\": exec: \"helm\": executable file not found in $PATH"
exiting dev mode because first deploy failed: failed to determine binary version: helm version command failed "": exec: "helm": executable file not found in $PATH
```

⇒ helm 설치`brew install helm`
but, 또 다른 문제에 직면 .. ..

```jsx
coalesce.go:196: warning: cannot overwrite table with non table for repository (map[REPOSITORY:<nil>])
Error: UPGRADE FAILED: failed to create resource: Service "map[NAME:<nil>]" is invalid: [metadata.name: Invalid value: "map[NAME:<nil>]": a DNS-1035 label must consist of lower case alphanumeric characters or '-', start with an alphabetic character, and end with an alphanumeric character (e.g. 'my-name',  or 'abc-123', regex used for validation is '[a-z]([-a-z0-9]*[a-z0-9])?'), spec.selector: Invalid value: "map[NAME:<nil>]": a valid label must be an empty string or consist of alphanumeric characters, '-', '_' or '.', and must start and end with an alphanumeric character (e.g. 'MyValue',  or 'my_value',  or '12345', regex used for validation is '(([A-Za-z0-9][-A-Za-z0-9_.]*)?[A-Za-z0-9])?')]
exiting dev mode because first deploy failed: deploying "nearart-end-user-server": install: exit status 1
```

⇒ 

<additional>

- Skaffold 
****k8s 애플리케이션의 빌드/배포 관련 파이프라인을 정의하고 실행하는 도구
반복적인 빌드/배포 파이프라인의 자동화를 제공하여 개발자가 개발에 더욱 집중 할 수 있도록 도와줌
관련 상세 정보들을 설정파일 skaffold.yaml 로 정의
skaffold를 개발 환경에서 실행 하면 소스 코드의 변경을 감시하고, 소스 코드의 변경이 감지되면 자동으로 설정된 파이프라인을 실행하여 빌드/배포 수행
1. 소스 코드 변경 주기적으로 감시
2. 소스 코드 기반으로 컨테이너 이미지 빌드 (Docker)
3. 빌드 된 컨테이너 이미지의 유효성 테스트
4. 빌드 된 컨테이너 이미지에 태깅 
5. 태깅 된 컨테이너 이미지를 컨테이너 이미지 저장소에 저장 (push)
6. k8s 클러스터에 배포 
skaffold의 실행은 skaffold.yaml이 있는 경로에서 명령어를 통해 실행 할 수 있음
`skaffold dev` : 소스 코드의 변경이 발생하는 지를 감시하고, 소스 코드 변경 발생 시 정의 해놓은 파이프라인을 자동으로 실행 
⇒ intellij "Develop on Kubernetes"
`skaffold run` : 정의 해놓은 파이프라인을 일회성으로 실행
⇒ intellij "Run on Kubernetes"
- Helm 
k8s 를 package로 관리 해주는 package manager (Node.js의 npm 역할)
helm chart : helm의 package format, k8s를 설명하는 파일들의 집합

## 스터디에 참조한 문서

[https://cloud.google.com/code](https://cloud.google.com/code)
[https://gc.hosting.kr/blog-msa_googlecloudcode-2/](https://gc.hosting.kr/blog-msa_googlecloudcode-2/)
[https://cloud.google.com/code/docs/intellij/using-a-k8-existing-app](https://cloud.google.com/code/docs/intellij/using-a-k8-existing-app)
[https://medium.com/google-cloud-apac/gcp-skaffold-그리고-cloud-code-65aad64ba782](https://medium.com/google-cloud-apac/gcp-skaffold-%EA%B7%B8%EB%A6%AC%EA%B3%A0-cloud-code-65aad64ba782)
[https://cloud.google.com/code/docs?hl=ko#기능](https://cloud.google.com/code/docs?hl=ko#%EA%B8%B0%EB%8A%A5)
[https://www.youtube.com/watch?v=geGzJSI_SPs](https://www.youtube.com/watch?v=geGzJSI_SPs)
[https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin#setup](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin#setup)
[https://skaffold.dev/docs/references/yaml/](https://skaffold.dev/docs/references/yaml/)
[https://github.com/GoogleContainerTools/skaffold/blob/master/examples/jib-multimodule/pom.xml](https://github.com/GoogleContainerTools/skaffold/blob/master/examples/jib-multimodule/pom.xml)
