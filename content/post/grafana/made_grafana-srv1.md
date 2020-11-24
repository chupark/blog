---
title: "Azure 리소스 모니터링 서버 만들기 (1)"
date: 2020-11-24T21:29:55+09:00
categories:
  - "Monitoring"
tags:
  - "Grafana"
  - "Azure"
---

## Azure 기본 모니터
----
Azure 모니터는 Azure 뿐만 아니라 On-premise, 다른 클라우드 환경의 모니터링 데이터를 수집, 분석할 수 있으며 여러가지 알림을 보낼 수 있습니다. Azure에서 VM을 만들 경우 아래 그림처럼 VM의 모니터링 블레이드에서 기본적으로 CPU, 디스크, 네트워크 메트릭을 확인할 수 있습니다. 
{{< figure src="/img/grafana/azure-monitor.png" title="" >}}

아쉽게도 메모리를 확인하려면 OMS Agent 혹은 VM Guest 에이전트를 설치해야 합니다.  
모니터링 대시보드를 만드는 과정은 약간 처참하지만, 시간을 들이면 아래와 같은 화면을 만들 수도 있습니다.

{{< figure src="/img/grafana/azure-workbooks.png" title="" >}}
위의 대시보드는 Azure의 Workbooks 기능으로 Azure Metric API가 제공하는 데이터를 사용하여 만들어집니다. Workbooks는 몇 번의 클릭으로 만들 수 있으며, Log Analytics + Qusto 쿼리를 조합하여 만들 수도 있습니다. 말로 설명하니 엄청 쉬워보이지만, 실제로 만들어보면 아래처럼 엄청 복잡한 작업과 노가다가 필요한 작업 입니다. Workbooks를 사용하여 모니터링 대시보드를 만드는 방법은 언제가 될지 모르겠지만.. 추후 포스트에서 다뤄보도록 하겠습니다.

{{< figure src="/img/grafana/azure-workbooks2.png" title="" >}}

이 방법의 장점이 있다면, 단순히 Azure Metric API 데이터를 Workbooks로 표현할 경우 완전 무료라는 것 입니다. 또한 시간을 들인만큼 시인성이 뛰어난 화면을 만들 수 있단 것 입니다. 반면, 단점을 뽑아보자면 대시보드내 검색기능 불가, 대시보드 고정 불가 등.. Grafana에 비해 보여지는 단점들이 너무 많습니다. 하지만 앞서 말했듯... 무료 라는 아주 큰 강점이 있습니다.  

<br/>

아래 사진은  Workbooks를 하나의 공유 대시보드에 Pin을 한 상태 입니다. 먼저 큰 틀을 살펴보면 대시보드 수준의 검색기능이 없습니다. 박스 안의 검색 기능은 해당 박스 내에서만 작동하므로, 여러 모니터링 패널이 있을 경우 통합 검색이 불가능 합니다. 여기서 재밌는 점은 Workbooks는 기본적으로 통합 검색을 지원하는데, 해당 Workbooks 전체를 대시보드에 Pin을 할 경우 맨 아래 작은 박스처럼 Workbooks로 넘어가는 링크 이미지만 생성됩니다.
{{< figure src="/img/grafana/azure-monitor-dashboard.png" title="" >}}  

　
<br/>
<br/>

## Azure with Grafana 모니터링
----
포스트의 주제인 Azure with Grafana를 이제부터 알아보겠습니다. Grafana 모니터링을 사용하면 Azure Monitor에서 있었던 다소 아쉬운 부분들을 커버할 수 있습니다. 먼저 Azure 대시보드를 만드는 것 보다 방법이 매우 쉽습니다. 몇 가지 설정을 하고 클릭 조금이면 훌륭한 대시보드를 만들 수 있습니다. 또한 대시보드 수준 검색 기능을 제공하여 원하는 데이터를 한 눈에 확인할 수 있는 장점이 있습니다.  
{{< figure src="/img/grafana/grafana1.png" title="" >}}  
<br/>

### 데이터는 어디에 저장되는데?
Grafana를 접해보신 분들이라면 대시보드를 만들기 위해 먼저 데이터 원본을 연결하고, 패널을 만들었을 것 입니다. Azure with Grafana 구성에서는 데이터를 따로 저장하지 않습니다. Azure에서 기본으로 제공하는 Metric API를 쿼리하여 데이터를 가져오기 때문에, 메트릭 저장용 DB가 필요 없습니다. 다만 Azure 데이터를 쿼리 해오기 위한 Service Account(Azure에선 서비스 주체) 즉, Azure AD App이 필요합니다. 그림으로 표현하면 아래와 같습니다.

{{< figure src="/img/grafana/azure-aad1.png" title="" >}}  

또한, 아래 그림처럼 여러 테넌트의 구독에 있는 리소스의 메트릭을 하나의 Grafana 서버에서 표현할 수도 있습니다.

{{< figure src="/img/grafana/azure-aad2.png" title="" >}}  
<br/>

### 사용자 관리와 보안은?
Grafana는 자체적으로 로그인 기능을 지원합니다. 또한 Oauth도 지원하여 Microsoft, Google등 외부 디렉토리 서비스와 통합하여 사용할 수 있습니다. Azure AD 로그인 기능을 예로 들면, Azure AD에 등록된 사용자만 Grafana 서버에 로그인 할 수 있도록 구성할 수 있습니다. 또한 테넌트가 여러개일 경우 Organization 기능을 사용하여 테넌트별로 데이터소스와 대시보드를 구성할 수 있습니다. 그리고 각 Organization에 구성된 사용자별로 Admin, Editor, Viewer 3가지 권한을 부여할 수 있습니다. Organization 기능은 Grafana의 빌트인 기능 입니다.
{{< figure src="/img/grafana/grafana-login.png" title="" >}}  

　
<br/>
<br/>

## Azure with Grafana 서버 구성하기
----
위에서 몇 가지 기본 사항을 알아봤으니 다음 포스트 부터는 실제로 서버를 구성해보도록 하겠습니다.  
컨텐츠는 아래 목차로 진행될 예정 입니다.
- Grafana 설치 & Azure 플러그인 사용
- HTTPS, Grafana 로그인에 Azure AD 인증 사용
- Slack 알림 구성