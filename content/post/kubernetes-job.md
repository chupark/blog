---
title: "Kubernetes Job"
date: 2020-09-03T14:53:38+09:00
categories:
  - "kubernetes pod"
tags:
  - "kubernetes"
comments: true
draft: true
---

# Job, 잡
Kubernetes는 계속 실행하는 포드가 아닌 작업이 완료되면 종료되는 포드가 있다. 레플리카셋 데몬셋 등 리소스는 포드를 계속 실행시키지만, 잡은 작업이 완료되면 포드를 종료시킨다.  
　
<br>

## 기본
----
잡은 프로세스가 완료되어도 컨테이너가 다시 시작되지 않도록 하는 포드를 실행한다. 노드 장애가 발생하면 레플리카셋과 마찬가지로 다른 노드로 다시 스케줄된다. 프로세스 자체 오류가 발생하는 경우, 잡은 컨테이너를 다시 시작하도록 구성할 수 있다. 개별 포드로도 태스크를 실행하고 완료될 때 까지 기다릴 수 있지만, 작업 시간동안 노드 장애 등의 원인으로 인해 태스크를 정상적으로 마치지 못했을 경우 사용자가 이를 감지하고 수동으로 작업을 다시 생성해야한다. 보통 이러한 태스크는 매우 오래 걸리므로 사용자가 수동으로 태스크를 다시 시작하는건 어렵다고 봐야한다.  

<br>

특정 그룹의 노드에 데몬셋 포드를 배포하고 싶을 경우, 데몬셋의 포드 템플릿에서 node-Selector 속성을 지정하여 원하는 노드에만 포드를 배포할 수 있다.
　  
<br>
　  
### 잡 정의
----
아래 템플릿은 2분간 sleep을 수행하고 완료되는 잡을 만든다.
````yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: batch-job
spec:
  template:
    metadata:
      labels:
        app: batch-job
    spec:
      restartPolicy: OnFailure
      containers:
      - name: main
        image: luksa/batch-job
````

{{< figure src="/img/job/1.png" title="" >}}

잡이 완료되면 포드는 Completed 상태가 되며 태스크의 로그를 확인할 수 있도록 포드는 자동으로 삭제되지 않는다.

{{< figure src="/img/job/2.png" title="" >}}

　
<br>

### 다수의 포드 인스턴스 실행
----
잡은 두 개 이상의 포드 인스턴스를 만들고 병렬 또는 순차적으로 실행하도록 구성할 수 있다. 잡 스펙의 completions와 parallelism 속성을 설정하여 수행한다.

#### 직렬 수행
----
