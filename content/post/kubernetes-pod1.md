---
title: "Kubernetes Pod"
date: 2020-09-01T15:56:21+09:00
categories:
  - "kubernetes"
tags:
  - "kubernetes"
comments: true
---

# Pod, 포드
Pod는 Kubernetes 애플리케이션의 배포 단위이다. Pod 안에는 여러 컨테이너를 배치할 수 있으며 같은 Pod는 같은 네임스페이스를 공유하기 때문에 hostname, ip등을 공유하게 된다.  
　
<br>

## 기본
----
포드는 컨테이너의 공동의 배치 그룹이며 Kubernetes의 기본 빌딩 블록을 대표한다. Kubernetes는 컨테이너를 개별적으로 배포하는 대신 항상 컨테이너의 포드를 배포하고 운영한다. 포드는 일반적으로 단일 컨테이너를 포함하지만 여러 개의 컨테이너를 포함하여 배포할 수 있다. 같은 포드 내의 컨테이너는 동일한 워커노드에서 실행된다. 포드에는 볼륨이라는 저장소를 마운트하여 포드 내의 컨테이너가 볼륨을 공유하며 사용할 수 있다.
![Pod](https://d33wubrfki0l68.cloudfront.net/fe03f68d8ede9815184852ca2a4fd30325e5d15a/98064/docs/tutorials/kubernetes-basics/public/images/module_03_pods.svg)

<br>

## 프로브
----
포드가 노드에 스케줄되는 대로, 해당 노드의 Kubelet은 컨테이너를 실행하고 포드가 존재하는 동안 계속 실행시키도록 노력한다. 컨테이너의 주 프로세스에 크래시가 발생하면 Kubelet이 컨테이너를 다시 시작한다. 애플리케이션 버그가 발생하여 컨테이너 크래시가 발생해도 Kubelet은 컨테이너를 자동으로 다시 시작하므로 쿠버네티스에서 실행 중인 애플리케이션을 자동으로 복구할 수 있다.  
하지만 때론, 애플리케이션이 프로세스의 크래시없이 작동이 중단되는 경우도 있다. 예를 들어, 메모리 누수가 있는 자바 애플리케이션은 OOMErrors를 던지기 시작하지만 JVM프로세스는 계속 실행된다. 이러한 상황에선 애플리케이션 재시작을 위해 특별한 검사를 실행해야한다.  
　
<br>

### 라이브니스 프로브
----
쿠버네티스는 세 가지 메커니즘 중 하나를 사용하여 컨테이너를 검색한다.
1. **HTTP GET** 프로브는 지정한 IP, 포트, 경로에 HTTP GET 요청을 수행한다. 프로브가 응답을 수신하고 응답 코드가 오류를 나타내지 않을 경우(HTTP status : 2XX, 3XX) 프로브는 성공한 것으로 간주한다. 프로브가 실패하면 컨테이너를 재시작한다.
2. **TCP** 소켓 프로브가 컨테이너의 지정된 포트에 TCP 연결을 시도하고, 성공적으로 연결되면 프로브가 성공한 것이고 그렇지 않으면 컨테이너가 재시작된다.
3. **Exec** 프로브는 컨테이너 내부에 임의의 명령을 실행하고 명령의 종료 상태 코드를 확인한다. 상태코드가 0이면 검사가 성공적이다. 다른 모든 코드는 오류로 간주된다.  
    ````yaml
    apiVersion: v1
    kind: Pod
    metadata:
        name: pod
    spec:
        containers:
        - image: my-image
        name: my-pod
        livenessProbe:
            httpGet:
              path: /
              port: 8080
    ````
    > **_NOTE:_**  라이브니스 프로브가 실패하면, 컨테이너가 강제 종료되며 완전히 새로운 컨테이너가 생성된다. 기존 컨테이너가 재시작되는 것이 아니다.

　  
<br>

#### 라이브니스 프로브의 추가 속성
----
위의 예시의 라이브니스 프로브 옵션 외에도 초기 지연 시간, 시간 초과, 기간 등과 같은 추가 속성을 지정할 수 있다. 
````yaml
apiVersion: v1
kind: Pod
metadata:
  name: kubia-liveness
spec:
  containers:
  - image: luksa/kubia-unhealthy
    name: kubia
    livenessProbe:
      httpGet:
        path: /
        port: 8080
      initialDelaySeconds: 15
      periodSeconds: 10
      timeoutSeconds: 1
      successThreshold: 1
      failureThreshold: 3
````
- **initialDelaySeconds** : 첫번째 프로브 지연시간.
- **periodSeconds** : 프로브 간격.
- **timeoutSeconds** : 프로브가 이 시간동안 응답이 없으면 failureThreshold 값을 증가시킨다.
- **successThreshold** : 프로브 실패 후 다시 성공으로 간주하는 응답 수. 라이브니스에선 1로 설정한다.
- **failureThreshold** : 프로브가 이 횟수만큼 실패하면 컨테이너를 다시 만든다.

　  
<br>

### 스타트업 프로브
----
초기 구동 시간이 매우 긴 애플리케이션을 위한 프로브로, 스타트업 프로브 시간동안 프로브 헬스체크에 성공하면 라이브니스 프로브로 프로브가 전환된다.
````yaml
apiVersion: v1
kind: Pod
metadata:
  name: kubia-liveness
spec:
  containers:
  - image: luksa/kubia-unhealthy
    name: kubia
    livenessProbe:
      httpGet:
        path: /
        port: 8080
      initialDelaySeconds: 15
      periodSeconds: 10
      timeoutSeconds: 1
      successThreshold: 1
    startupProbe:
      httpGet:
        path: /
        port: 8080
      failureThreshold: 30
      periodSeconds: 10
````
스타트업 프로브는 10초 * 30회로 300초의 스타트업 프로브 시간을 가진다. 스타트업 프로브 시간동안 400번 이하의 HTTP Status Code가 반환되면 스타트업 프로브를 성공으로 처리하고 라이브니스 프로브로 전환된다.

　  
<br>

### 레디네스 프로브
----
애플리케이션이 초기 구동이 오래 걸리거나, 시작 직후 외부 의존성 때문에 요청을 처리하지 못할 수 있다. 이 경우 운영자는 컨테이너를 종료하고 싶지않고 요청도 보내고 싶지 않는다. Kubernetes는 이러한 상황을 감지하고 완화하기위한 레디네스 프로브를 제공한다. 컨테이너가 준비되지 않았다고 보고한 포드는 Kubernetes Services를 통해 트래픽을 수신받지 않는다.
````yaml
apiVersion: v1
kind: Pod
metadata:
  name: kubia-liveness
spec:
  containers:
  - image: luksa/kubia-unhealthy
    name: kubia
    livenessProbe:
      httpGet:
        path: /
        port: 8080
      initialDelaySeconds: 15
      periodSeconds: 10
      timeoutSeconds: 1
      successThreshold: 1
    startupProbe:
      httpGet:
        path: /healthz
        port: liveness-port
      failureThreshold: 30
      periodSeconds: 10
    readinessProbe:
      httpGet:
        path: /
        port: 8080
      initialDelaySeconds: 5
      periodSeconds: 5
````
레디네스 프로브는 라이브니스 프로브와 같이 사용이 가능하다. 레디네스 프로브가 응답이 없으면 Kubernetes Service의 백앤드에서 제외가 될 것이고, 라이브니스 프로브가 응답이 없으면(애플리케이션 크래쉬) 컨테이너가 다시 만들어질 것 이다.