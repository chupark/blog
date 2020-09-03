---
title: "Kubernetes Replicaset"
date: 2020-09-02T15:50:46+09:00
categories:
  - "kubernetes"
tags:
  - "kubernetes"
comments: true
---

# Replicaset, 레플리카셋
레플리카셋은 포드의 복제본을 일정한 수의 복제본으로 유지하려고 노력하는 Kubernetes 리소스다. 최초에는 레플리케이션 컨트롤러가 포드를 복제하고 노드가 실패했을 때 재스키줄링하는 유일한 Kubernetes 구성 요소였다. 나중에 레플리케이션 컨트롤러보다 더 진보된 레플리카셋이라는 리소스가 도입됐다.  
　
<br>

## 기본
----
레플리카셋은 레플리케이션컨트롤러와 똑같이 동작하지만 다양한 포드 셀렉터를 갖는다. 레플리케이션 컨트롤러는 단일 셀렉터만 사용해 포드를 매칭시켰지만, 레플리카셋은 특정 라벨이 있거나 없거나와 여러개의 라벨을 일치시킬 수 있다. 또한 env=*과 같이, 특정 라벨 키를 가진 모든 포드를 매칭시킬 수도 있다.  
　
<br>

### 레플리카셋 정의
----
레플리카셋이 레플리케이션 컨트롤러와 다른점은 셀렉터다. 포드는 selector 속성 바로 아래에 있지 않고, selector.matchLabels 아래에 지정한다.
````yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: kubia
spec:
  replicas: 3
  selector:
    matchLabels:
      app: kubia
  template:
    metadata:
      labels:
        app: kubia
    spec:
      containers:
      - name: kubia
        image: luksa/kubia
````
　
<br>

레플리카셋은 레플리케이션컨트롤러와 다르지 않다. 셀렉터와 일치하는 세 개의 복제본이 있음을 보여준다.
{{< figure src="/img/replicaset/1.png" title="" >}}
　
<br>

#### 레플리카셋의 다양한 라벨 셀렉터 사용
----
레플리카셋의 주요 개선점은 표현식이 뛰어난 라벨 셀렉터다. 아래 처럼 matchExpression 속성을 사용하도록 selector를 작성할 수 있다.
````yaml
apiVersion: apps/v1beta2
kind: ReplicaSet
metadata:
  name: kubia
spec:
  replicas: 3
  selector:
    matchExpressions:
      - key: app
        operator: In
        values:
         - kubia
  template:
    metadata:
      labels:
        app: kubia
    spec:
      containers:
      - name: kubia
        image: luksa/kubia
````
　  
레플리카셋은 포드를 항상 정의된 복제본 수 만큼 유지하도록 노력한다. 라벨이 일치하는 포드가 적으면 새로운 복제본을 생성하고 더 많으면 포드를 삭제하여 항상 정의된 수를 유지한다. 
{{< figure src="/img/replicaset/2.png" title="" >}}
> **_NOTE :_** 사진을 살펴보면 포드는 이미 3개가 만들어졌지만 1개만 준비된 상태임을알 수 있다.

레플리카셋은 아래와 같은 연산자를 가진다.
- **IN** : 라벨의 값이 지정된 값 중 하나와 일치해야 한다.
- **NotIn** : 라벨의 값이 지정된 값과 일치해서는 안 된다.
- **Exists** : 포드에는 지정된 키가 있는 라벨이 포함돼야 한다 (값은 중요하지 않음). 이 연산자를 사용할 때 값 필드를 지정하면 안 된다.
- **DoesNotExist** : 포드에는 지정된 키가 있는 라벨을 포함하면 안 된다. values 속성을 지정하면 안 된다. 여러 표현식을 지정하면 셀렉터가 포드와 일치하도록 모든 표현식이 true로 평가돼야 한다.

> **_NOTE :_** matchLabels와 matchExpressions을 모드 지정하면 셀렉터와 일치호도록 모든 라벨이 일치해야 하고 모든 표현식이 true로 평가돼야 한다.

