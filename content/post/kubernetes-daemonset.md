---
title: "Kubernetes Daemonset"
date: 2020-09-03T11:06:22+09:00
categories:
  - "kubernetes pod"
tags:
  - "kubernetes"
comments: true
---

# Daemonset, 데몬셋
레플리카셋은 Kubernetes 클러스터의 특정 위치에 배포된 특정 개수의 포드를 실행하는 데 사용된다. 그러나 노드 로그 수집기, 노드 리소스 모니터 수집기 등 각 노드에서 정확히 하나의 포드를 실행해야 하는 경우가 있다. 이 경우 데몬셋을 사용한다.  
　
<br>

## 기본
----
데몬셋에 의해 만들어진 포드는 이미 대상 노드가 지정돼 욌고 Kubernetes 스케줄러를 패스한다. 데몬셋은 레플리카의 개념이 없고 노드가 있는 수만큼 포드를 생성하고 노드 각각에 포드를 하나씩 배포한다. 노드가 다운되면 데몬셋은 다운된 노드의 포드를 다른 노드로 스케줄하지 않는다. 반대로 노드가 새로 생성되면 즉시 새 포드를 배포한다. 누군가 포드를 삭제하여 노드에 데몬셋의 포드가 없는 상태가 되어도 마찬가지다.  

<br>

특정 그룹의 노드에 데몬셋 포드를 배포하고 싶을 경우, 데몬셋의 포드 템플릿에서 node-Selector 속성을 지정하여 원하는 노드에만 포드를 배포할 수 있다.
　  
<br>
　  

### 데몬셋 정의
----
아래 예시처럼 SSD를 사용하는 노드가 있다고 가정하고 5초마다 "SSD OK"를 표준 출력에 보여주는 ssd-monitor 프로세스를 실행하는 데몬셋을 생성해본다.  
````yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: ssd-monitor
spec:
  selector:
    matchLabels:
      app: ssd-monitor
  template:
    metadata:
      labels:
        app: ssd-monitor
    spec:
      nodeSelector:
        ssd: "true"
      containers:
      - name: main
        image: luksa/ssd-monitor
````

<br>

그 결과 아래 그림처럼 SSD=true 라벨을 가진 노드에 데몬셋에 의해 만들어진 포드가 배포됐음을 확인할 수 있다. 만약 노드의 라벨이 SSD=false로 변경되거나 라벨이 삭제되면 해당 노드에 있는 데몬셋 포드는 삭제된다.

{{< figure src="/img/daemonset/2.png" title="" >}}